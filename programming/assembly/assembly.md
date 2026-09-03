# Registers




# Memory

0x00000000  mov rax, #1
0x00000004  svc #0

**why instruction in memory jumps from 00 to 04?**
- each memory address contains 1 byte (8bit) of space.
- each assembly instruction requires 4 bytes (32 bits) of size.
- so each assembly instruction in memory works in a 4 byte set. this is same for 32 bit as well as 64 bit computers.


### hello.asm code
```asm
.global _main // this tells the linker where the program's execution begins.

_main:
	// print hello world
	mov x0, #1 // stdout
	ldr x1, =hello // buffer pointer
	mov x2, #len // size int
	mov x8, #64 // write syscall
	svc #0

	// exit syscall
	mov x0, #69 // error code
	mov x7, #1 // exit syscall
	svc #0

.data
	hello: .ascii "Hello, world!\n"
	len = . - hello // {current address} - {starting address of hello} = len
```