Popular Languages in Embedded Programming
- C & C++
- Rust
- Assembly - Look at FFMpeg for benefits
- Java
- Python (C Based)

C is still the most popular by a mile.

## C Programming Standardization
- Developed in AT&T
- Became popular but with no standards
- Non-official standard was K&R C
- First official international standard was released in 1990, and was called C90.
- In 1999, C99 was released.
- You can write older C code and compile it in newer versions, but not vice versa. 

## Notes
- Build creates the Binary, Clean removes it
- Whats a cross-compiler? 
	- A cross-compiler is a special compiler that runs on one type of computer system (the host) but generates executable code for a different system (the target) with a different architecture or operating system, like compiling code for a smartphone (ARM) on a PC (x86)
- What is #include? 
	- like a library, it is a preprocessor directive that instructs the preprocessor of thte compiler to include a file into the source file. 
- What is a pre-processor? 
	- A preprocessor is a tool that processes your source code before the actual compilation begins, handling tasks like macro expansion, file inclusion, and conditional compilation.
	- The preprocessor operates as a separate step in the software development pipeline. When you write C or C++ code, the preprocessor runs first, modifying a copy of your source file before it reaches the compiler. All preprocessor directives begin with a '#' (hash) symbol, which signals that the statement should be processed by the preprocessor rather than the compiler
- -save-temps saves all the files in the compilation process, showing how the include and all the code compile together. 
	- `main.c -> main.s -> compiler (Assembler stage) -> main.o (relocatable object file)
- A union is a struct that puts memory next to one another, not allowing padding . It consumes less data, and is only used when there is mutally exclusive data, one exists or the other, never at the same time. Thats when you should use unions instead of structs. 
	- Applicability of unions is for
		1. Bit Extraction
		2. Storing mutually exclusive data, thus saving memory
	- Put a struct inside a union with the struct fields for easy access of nested data. 

#### Printing on M4 Cortex
- printf works over SWO pin (Serial Wire Output) pin of SWD interface
	- Serial Wire Output (SWO) is a single-pin output protocol in ARM Cortex-M microcontrollers that enables real-time tracing, logging, and debug data emission from firmware without using peripherals like UART or USB. 
	- Serial Wire Debug (SWD) is a 2-pin (SWDIO for bidirectional data, SWCLK for clock) debugging protocol developed by ARM as part of its CoreSight architecture, serving as a low-pin-count alternative to the 4-5 pin JTAG interface for Cortex-A/M/R cores in MCUs, MPUs, and SoCs. 
		- It enables external debug probes (e.g., J-Link, ST-Link) to connect via the Debug Access Port (DAP), comprising a Debug Port (DP) that interfaces with pins and multiple Access Ports (APs)—like AHB-AP for memory-mapped access to flash, SRAM, registers, and peripherals—allowing halt/resume, breakpoints, memory read/write, and programming
- Microcontroller: A microcontroller (MCU) is a compact integrated circuit that functions as a small computer on a single chip, integrating a processor, memory, and input/output (I/O) peripherals to control specific tasks in embedded systems.
	- The ST-LINK/V2 (including variants like V2/01-0 or Mini) is an in-circuit debugger and programmer tool from STMicroelectronics for STM8 and STM32 microcontrollers, using SWIM (for STM8) and JTAG/SWD (for STM32) interfaces to program firmware and debug code on target boards. 
- **Instrumentation Trace Macrocell Unit**
	- This is an optional application driven trace source (It enables developers to create named trace sources (e.g., "BusinessLogic" or "Validation") for different code areas, allowing independent control of logging levels and outputs.) that supports printf style debugging to trace operating system and application events, and generates diagnostic system information.

#### SWD (Serial Wire Debug)
- A two-wire protocol for accessing the ARM debug interface
- It is part of the ARM debug interface specification v5 and is an alternative to JTAG
	- JTAG (Joint Test Action Group) is an industry-standard hardware interface (IEEE 1149.1) for testing, debugging, and programming electronic circuits, such as printed circuit boards (PCBs), microcontrollers, FPGAs, and embedded systems. Needs more pins than SWD (4 down to 2). 
- The physical layer of SWD consists of 2 lines
	- SWDIO: a bidirectional data line
	- SWCLK: a clock driven by the host
- SWD interface lets you program MCUs internal flash, memory regions, add breakpoints
- You can use the serial wire view for your printf statements for debugging
- SWD has 2 pins for debug, and a pin for trace. 
- SWO Pin is connected to ST link circuitry of the board and can be captured using our debug software (IDE), not all IDES support this. 

#### ITM  (Instrumentation Trace Macrocell) 
- The ITM (Instrumentation Trace Macrocell) on STM32 (Cortex-M3+) uses a shared FIFO buffer (typically 10 bytes) to queue data packets from multiple stimulus ports in a first-in-first-out (FIFO) manner before outputting them via the trace bus to SWO (Serial Wire Output).
	- **Data Entry**: Software writes data (e.g., via printf or ITM->PORT) to a stimulus port register (e.g., ITM_STIMULUS_PORT0 at 0xE0000000). This creates a packet added to the common FIFO
	- **FIFO Check**: Before writing, poll bit 0 of the port register (ITM_STIMULUS_PORT0 & 1) to ensure space; if empty (0), wait to avoid overflow.

#### What is UART
- UART stands for Universal Asynchronous Receiver-Transmitter, a hardware protocol for asynchronous serial communication between devices like microcontrollers, computers, and peripherals. 

#### What is created when cross compiling for STM32?
- **.elf**: Executable and Linkable Format file containing the full linked program with debug symbols, sections, and metadata, produced by the linker (e.g., arm-none-eabi-gcc -o program.elf).
- **.bin**: Raw binary image of the program code/data (no headers/metadata), generated from .elf via objcopy for direct flash memory loading.
- **.hex**: Intel HEX format text file representing the binary data in ASCII for easy programming via tools like ST-LINK or bootloaders. 

#### What is the GNU Arm Embedded Toolchain? 
- The GNU Arm Embedded Toolchain is a ready-to-use, open-source suite of tools for C, C++, and assembly programming targeting 32-bit Arm Cortex-A, Cortex-M, and Cortex-R processors. 1
- It includes the GNU Compiler Collection (GCC), along with components like a linker, assembler, and debugger, forming a cross toolchain for embedded development on bare-metal systems or without an OS. 

#### Embedding Project Build Process
1. Preprocessing (**.c **→** .i**)**
	- Expands macros, resolves #include directives by inserting header file contents, removes comments, and handles conditional compilation (e.g., #ifdef). Output is pure C code without directives, saved as .i files (use gcc -E file.c -o file.i).
2. Compilation (.i → .s)
	- Parser and compiler analyze the preprocessed code for syntax/semantic errors, perform type checking, and generate equivalent **assembly code** (mnemonics like mov, call). Output is .s files (use gcc -S file.i -o file.s). This is where "code correctness" is verified.
	- Key checks: Variable declarations, function signatures, control flow.
 3. Assembly (.s → .o)
	- Assembler translates assembly mnemonics into **machine code** (binary instructions), but leaves external references (e.g., printf) as unresolved symbols. Produces **object files** (.o), one per .c file, containing relocatable machine code. 
	- **Note**: No linking yet—function calls use placeholders. 
4. Linking (.o files + libraries → executable)
	- Linker combines multiple .o files, resolves external symbols (e.g., links printf from libc), performs **relocation** (adjusts addresses for final layout), and embeds libraries. Output is the **final executable** (e.g., a.out or named binary like ELF/PE format). 
	- Types: Static (embeds libraries at link time) vs. dynamic (loads at runtime).
5. Execution (optional post-compilation)
	- OS loader maps the executable into memory, resolves dynamic links, initializes stack/heap, and jumps to main(). Output runs, producing results (e.g., ./executable).

#### What is A Microcontroller? 
- MCU (MicroController Unit) is a small computer system on a single chip. But its resources and capabilities such as memory, speed, external interfaces are very much limited than a desktop PC because of MCU targets embedded applications. 
- A typical microcontroller includes a processor, volatile and non-volatile memories, input/output (I/O) pins, peripherals, clock, bus interfaces on a single chip.
- 