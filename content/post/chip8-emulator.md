+++
title = "Building a CHIP-8 Emulator (C++)"
description = ""
tags = [
    "emulator", "c++", "virtual machine",
]
date = "2024-10-03"
categories = [
    "c++", "low-level"
]
+++

Building a CHIP-8 emulator is a great way to understand emulation concepts, low-level programming, and virtual machines. I want to share a basic outline to help you get started if you're a newcomer in C++ and I will include all the key components you'll need to implement.

### What is CHIP-8?
CHIP-8 is a simple interpreted programming language that was designed for early microcomputers. It has a straightforward architecture with the following key components:

- Memory: 4 KB of RAM.
- Registers: 16 8-bit data registers (V0 to VF).
- Stack: A stack for managing function calls (up to 16 levels).
- Display: A 64x32 pixel display.
- Input: A hexadecimal keypad with 16 keys.

These features make CHIP-8 an excellent candidate for emulation, providing a manageable complexity for developers.

### Structure
Before coding, it's important to set up a clean project structure. 

Here’s my recommended layout:
```bash
/chip8-emulator
├── /src
│   └── main.cpp
│   └── chip8.cpp
│   └── chip8.h
├── /assets
│   └── roms
├── /build
└── CMakeLists.txt (if using CMake)
```
### Defining the CHIP-8 structure
Defining the constants and structure that will represent our CHIP-8 system.

```cpp
// chip8.h
#ifndef CHIP8_H
#define CHIP8_H

const int MEMORY_SIZE = 4096;
const int DISPLAY_WIDTH = 64;
const int DISPLAY_HEIGHT = 32;
const int STACK_SIZE = 16;
const int NUM_REGISTERS = 16;

struct Chip8 {
    unsigned char memory[MEMORY_SIZE]; // 4KB memory
    unsigned char V[NUM_REGISTERS]; // 16 registers
    unsigned short I; // Index register
    unsigned short PC; // Program counter
    unsigned short stack[STACK_SIZE]; // Stack
    unsigned short SP; // Stack pointer
    unsigned char display[DISPLAY_WIDTH * DISPLAY_HEIGHT]; // Display
    bool key[16]; // Keypad state
};

void initialize(Chip8 &chip8);
void loadRom(Chip8 &chip8, const char* filename);
void emulateCycle(Chip8 &chip8);

#endif // CHIP8_H
```

### Initializing the Emulator
The initialization function sets up the registers, memory, and loads the CHIP-8 font set into memory.

```cpp
// chip8.cpp
#include "chip8.h"
#include <cstring>
#include <fstream>

void initialize(Chip8 &chip8) {
    chip8.PC = 0x200; // Programs start at 0x200
    chip8.I = 0;
    chip8.SP = 0;

    // Clear display
    std::memset(chip8.display, 0, sizeof(chip8.display));

    // Load font set
    unsigned char fontset[80] = {
        // 0-F
        0xF0, 0x90, 0x90, 0x90, 0xF0, // 0
        0x20, 0x60, 0x20, 0x20, 0x70, // 1
        // (Add the rest of the font set here)
    };
    std::memcpy(chip8.memory, fontset, sizeof(fontset));
}
```

### Loading the ROM files
Let's create a function to load a CHIP-8 ROM into memory.

```cpp
void loadRom(Chip8 &chip8, const char* filename) {
    std::ifstream file(filename, std::ios::binary);
    if (file) {
        file.read(reinterpret_cast<char*>(&chip8.memory[0x200]), MEMORY_SIZE - 0x200);
    }
}
```

The emulation cycle is where the magic happens. This loop continuously fetches instructions, decodes them, and executes them.

```cpp
void emulateCycle(Chip8 &chip8) {
    // Fetch
    unsigned short opcode = chip8.memory[chip8.PC] << 8 | chip8.memory[chip8.PC + 1];
    
    // Decode and Execute
    switch (opcode & 0xF000) {
        case 0x0000:
            if (opcode == 0x00E0) {
                // Clear the display
                std::memset(chip8.display, 0, sizeof(chip8.display));
                chip8.PC += 2;
            } else if (opcode == 0x00EE) {
                // Return from subroutine
                chip8.SP--;
                chip8.PC = chip8.stack[chip8.SP];
                chip8.PC += 2;
            }
            break;
        // (Add additional opcodes here)
        default:
            // Unknown opcode
            chip8.PC += 2;
            break;
    }
}
```

In our main function, initialize the emulator, load a ROM, and run the emulation loop.

```cpp
#include "chip8.h"
#include <iostream>

int main(int argc, char* argv[]) {
    Chip8 chip8;
    initialize(chip8);

    if (argc < 2) {
        std::cerr << "Please provide a ROM file." << std::endl;
        return 1;
    }

    loadRom(chip8, argv[1]);

    while (true) {
        emulateCycle(chip8);
        // Handle timing and rendering here
    }

    return 0;
}
```

### Additional Considerations
- CHIP-8 typically runs at about 60 Hz, so you'll need to implement a timer to control the speed of the emulator.
- To capture key presses, consider using libraries like SDL or SFML, which provide convenient ways to handle windowing and input.
- You'll need to create a window to display the 64x32 pixel output. Again, libraries like SDL can help you achieve this with minimal hassle.

That's it basically. Feel free to explore existing open-source CHIP-8 emulators for inspiration, and don't hesitate to reach out if you have any questions or need guidance. 
