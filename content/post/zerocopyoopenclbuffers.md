+++
title = "Zero Copy OpenCL Buffers"
description = ""
tags = [
    "gpu",
    "embedded",
    "opengl",
]
date = "2022-12-30"
categories = [
    "embedded",
]
+++

This is another subject that is very complicated to explain, but is crucial to understand for writing efficient code, especially on embedded systems and integrated GPUs. I figure to write it down as a reminder. This post mostly references Intel’s article on minimizing memory copy[1] and AMD’s ROCm OpenCL optimization guide[2].

[1: Getting the Most from OpenCL™ 1.2: How to Increase Performance by Minimizing Buffer Copies on Intel® Processor Graphics](https://www.intel.com/content/www/us/en/developer/articles/training/getting-the-most-from-opencl-12-how-to-increase-performance-by-minimizing-buffer-copies-on-intel-processor-graphics.html)

[2: ROCm documentation: OpenCL Optimization](https://rocmdocs.amd.com/en/latest/Programming_Guides/Opencl-optimization.html#pre-pinned-buffers)

When we first learn OpenCL, we learn that we allocate buffers using the `clCreateBuffer` function. Normally the function call looks like this `clCreateBuffer`(`ctx`, `CL_MEM_READ_WRITE`, `some_size`, `NULL, NULL`). The `CL_MEM_READ_WRITE` flag tells the underlying OpenCL runtime that the GPU want to be able to read and write to said buffer. Occasionally, a read or write only buffer is useful. And the runtime can optimize memory access base on that. Anyway, notice there’s more flags avaliable in the doc[3]. Namely `CL_MEM_USE_HOST_PTR`, `CL_MEM_ALLOC_HOST_PTR` and `CL_MEM_COPY_HOST_PTR`.

`khronos.org: clCreateBuffer`

These flags comes with cryptic descriptions in the documentation. In English, they control where the OpenCL buffer is located. By default the buffer lives on the VRAM. Thus the following code likely causes a DMA transfer from the host to the GPU, which can be slow at times as it involve system operations to allocate page-aligned memory then a DMA request. `malloc()` is hated because how slow it is even though it cached. A DMA is worse that there’s no way to cache it. According to AMD’s document. The buffer read/write calls can only provide about 2/3 of peak performance.

```c
cl_mem buffer = clCreateBuffer(ctx, CL_MEM_READ_WRITE, some_size, NULL, NULL);
// This can be slow!! Context switch is slow!
clEnqueueWriteBuffer(queue, buffer, CL_FALSE, 0, some_size, some_data, 0, NULL, NULL);
```

`CL_MEM_COPY_HOST_PTR` copies data to device at buffer creation. `CL_MEM_USE_HOST_PTR` might be `CL_MEM_ALLOC_HOST_PTR` but with user supplied memory. It’s implementation dependent. Both are quite useless in my opinion. `CL_MEM_ALLOC_HOST_PTR` is much more interesting.

### Optimizing OpenCL data transfer on integrated GPUs
`CL_MEM_ALLOC_HOST_PTR` asks OpenCL to allocate the buffer on the host memory instead on VRAM. On mobile devices and integrated GPUs, since the GPU shares the host memory. Can’t we just allocate the host memory and map it into GPU? Yes! That’s an optimization most vendors support. On an eligible device, again read your vendor’s documentation for details, runtimes often map host memory into the GPU upon kernel execution.

```c
cl_mem buffer = clCreateBuffer(ctx, CL_MEM_WRITE_ONLY | CL_MEM_ALLOC_HOST_PTR, some_size, NULL, NULL);
// This is low latency because buffer lives on the host memory
void* mapped = clEnqueueMapBuffer(queue, buffer, CL_TRUE, CL_MAP_WRITE, 0, some_size, 0, NULL, NULL, &err);
memcpy(mapped, some_data, some_size);
// Low latency because buffer is on the host memory
clEnqueueUnmapMemObject(queue, buffer, mapped, 0, NULL, NULL);
// Now OpenCL maps the host memory into the GPU. One system op for 2 operations.
clEnqueueNDRangeKernel(queue, kernel, ...);
```

This is faster because of two reasons, 1) The buffer now lives on somewhere the CPU can access. Thus mapping is basically free. And 2) OpenCL maps the CPU memory to GPU during kernel execution (an async operation). We can hide that latency unlike in plane buffer copy. In practice I’ve seen a streaming heavy application use 25% less CPU (dropping from 200% to 150% overall) by just changing the allocation flags and use mapping.

**Important**: Having the data located on main DRAM does not mean the CPU can access it. Usually there’s a hardware configured split for GPU and GPU memory. It’s likely the driver can map GPU memory into the CPU address space via the interconnect (vise versa). But direct access is not guaranteed. That’s why we need to tell OpenCL to make the buffer live on CPU memory and share it.

### Behavior on discrete GPUs and accelerators
The above benefit only works on integrated GPUs (or integrated DSP/FPGA). On discrete devices, where the device does not share the host memory, the above is likely not an optimization. As now we force the discrete GPU to DMA during kernel execution. Which is at least an order of magnitude slower compared to VRAM.

### FAQs
Does the reverse happen? Allocate VRAM and map it into host memory?

No, I haven’t seen it in the wild. Most likely because even on integrated GPUs, there are still dedicated chunks of memory for the GPU (split configured by BIOS/UEFI). If you take a look at Windows task manager, it has a shared and dedicated memory section for the GPU. The shared region is usually half of available memory for the system, and is used if `CL_MEM_ALLOC_HOST_PTR` is applied, otherwise the dedicated reagon.

Is there an API to check if zero copy is enabled?

No. This is an optimization that vendors and developers can take advantage of. But it’s not a requirement. Nor a part of the OpenCL standard. It has to be measured by testing the performance.

Can we check if the GPU shares the host memory?

Yes. `CL_DEVICE_HOST_UNIFIED_MEMORY` returns `CL_TRUE` if the device shares the host memory. Again, it’s not a guarantee that zero copy is enabled. It’s just a hint.
