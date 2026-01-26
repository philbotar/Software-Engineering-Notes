Keil UVision is used for Assembly, and CubeIDE is for C code. 

Microcontroller is a subset of the development board. The Development Board has the burg pins, the debugger, LEDs and etc. 

What is CMSIS?
- **CMSIS** (Cortex Microcontroller Software Interface Standard) is a vendor-independent hardware abstraction layer that provides consistent software interfaces for Arm Cortex-M (and some Cortex-A) processors, simplifying development, code reuse, and portability across microcontrollers.
	- Standardization: It defines common names for processor core registers and peripheral interfaces. This means NVIC_EnableIRQ(UART0_IRQn) works the same way regardless of who made the chip.
	- Modular Components: It isn't just one library; it’s a collection of modules, including:
		- CMSIS-CORE: The interface for the core processor and its basic peripherals.
		- CMSIS-RTOS: A generic API for Real-Time Operating Systems, allowing you to swap OSs (like FreeRTOS or Keil RTX) easily.
		- CMSIS-DSP: A library of over 60 highly optimized math functions (filters, matrix math, etc.) for Digital Signal Processing.
	- Reduced Learning Curve: Because the interface is consistent, once you learn how to program one Cortex-M device using CMSIS, you essentially know how to program them all.
	- Compiler Independent: It is designed to work with all major compilers, including GCC, IAR, and Arm’s own compiler.


