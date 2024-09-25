+++
title = "My problems with C++"
description = ""
tags = [
    "c/c++",
]
date = "2022-11-30"
categories = [
    "c/c++",
]
+++

I have been really annoyed by C++ recently. I love C++. It gives me the absolute best performance out of all languages with a high level of abstraction. But it has problems that makes it borderline unusable sometimes.

### The good
C++ gives me 100% control over very fine details on exactly when and how some low-level operation is performed. Unlike GC languages when reading data, with RAII. I can open a file with `std::ifstream`, read data then leave. I know RAII will close the file descriptor at the end of the function. Or I can manually scope the object to make it close early. No matter there’s some pesky exception. When writing a cache, C++ allocators allow me to decide an upper bound beforehand. Then react to too much data very rapidly and easily. Intel’s TBB provides very good concurrent containers that help tremendously remove mutexes from my code. Making my code blazingly fast and so on.

For the record. C++ is a very good language.

### The bad
And there are tons of problems I’ve been frustrated with.

- No *“it just works”* package manager
- Slow compilation without benefit

C++ doesn’t have a universally supported package manager. I understand this stems from the early days of the internet, where sharing source code is not a thing. And it’s the distro’s responsibility to package popular libraries. But damn it’s 2022. I still have to find some libraries on GitHub. Make it a submodule. Add CMake support for it. Projects like [conan](https://conan.io/) and [vcpkg](https://vcpkg.io/en/index.html) are trying to solve them. But they don’t *“just work”* like pip (Python) or Go does. Not to mention running the problem of two dependencies depending on different versions of the same library. Then either break ABI or ODR internally.

Without going into detail on how both work. Vcpkg supports installing multiple enviroments. But require users to `add -DCMAKE_TOOLCHAIN_FILE=/path/to/vcpkg/install/...` for every project that wants to build against Vcpkg’s installed field. This is definitely not a solution. Like, hello? No one is going to remember the path of the toolchain when they start a new project. Conan is smaller than vcpkg. Maybe because creating packages for Conan is harder. In any case, unlike Python and Rust devs. C++ developers do not have the habit of publishing their library on package managers.

One solution to all these, and one I use in production is [CPM](https://github.com/cpm-cmake/CPM.cmake). It is a CMake module that directly downloads repositories as modules and handles CMake for you. It’s more like how Rust’s cargo works. It populates libraries in the build directory. Then link them when building the project. However, it doesn’t solve the ABI/ODR issue. And it does not store the compiled result to be used by multiple projects. Making it unsuited for handling large dependencies like TensorFlow.

C++ is also notorious for it’s long compilation time. C++ grammar is context-dependent and ambiguous. Forcing the compiler to spend much time parsing and disambiguating. Furthermore, C++ compilers also spend a lot of time instantiating templates. C++ takes a no magic included approach. Only the most primitive types are defined by the compiler. Like char, int, pointers, and so on. Then everything is defined as an application of the fundamental types. In contrast languages like Java, Go, etc.. has compiler-defined array types (let me know if I’m wrong) and so on. This is a double-edged sword. This makes C++ very flexible while having to parse the standard library every time it gets included.

The second problem should be solved partially by C++20’s module feature. Yet the only compiler that fully supports modules is MSVC. Clang is still working on module support. While GCC seems to have no progress since GCC 11. GCC 12 release notes show no sign of their module support. This is the chance for C++ to solve its package and compilation issue. But the two major open source compilers haven’t supported the feature until 1 year from the next C++ version. Why?

### Solutions?
I’m seriously switching my primary language. However, I want whatever I choose to have the same performance guarantee C++ has. To be specific

- Gives me complete control over execution and resource allocation
- No virtual machine must compile to native binary
- Support custom allocators
- Have a package manager
- Easy way to call C function

Which rolls out a huge chunk of mainstream languages like C#, Golang, Scala, Haskell, Lisp, and so on… Currently I’m looking at Rust, Zig, and V.

Rust looks like the most promising option. It guarantees runtime safety at compile time. It is also the most popular language out of the three. However, Rust has its downsides. Namely, it does not have dynamic dispatch (i.e. virtual functions) which makes implementing some stuff more complicated. Rust is also very strict when compiling. This is good but Rust is evolving very fast. Sometimes making code suddenly doesn’t compile after update.

Zig is some really cool shit. Zig supports including C and C++ headers directly in Zig. Making utilizing C code in Zig very easy - I argue this is why C++ is so successful, able to reuse existing code. For example, I can directly invoke `epoll` without going through a binding. Zig’s build system is also nice. Instead of some config (Rust’s cargo) or scripting language (C++’s CMake). Zig build scripts are.. Zig! No more messy configs and no more stupid build scripts.

V is interesting that it compiles down to C. Like how Gnome’s Vala does. However, with emphasis on build speed. Compiling down to C makes V uniquely powerful. Instead generating LLVM IR directly. The Clang C compiler could generate much better LLVM IR from the equivalent C source code. V also supports (almost) straight up including C headers.

Am I going to switch off C++? No, not soon. But you bet I’ll be using a new language if the compilers didn’t solve the damn issues by the C++23 release.
