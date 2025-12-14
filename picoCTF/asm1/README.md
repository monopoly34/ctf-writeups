# asm1 - picoCTF 2019

------

## Introduction
asm1 is a Reverse Engineering task. The goal is to see what does `asm1(0x3ef)` returns.

Challenge description:

> What does asm1(0x3ef) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. Source

------

## Solution by manual analysis of the assembly code

Before starting to analyze the source code, here are some fundamentals of x86 assembly:

Unlike high-level programming languages, assembly relies on the use of **registers**, which are internal memory storage locations inside the processor.
Registers can range from 8-bit to 64-bit. The main difference is their naming convention (e.g., `al`/`ah` are 8-bit, `ax` is 16-bit, `eax` is 32-bit, and `rax` is 64-bit).

Since this challenge uses 32-bit general-purpose registers, here is a breakdown of their common uses:
  - **eax/EAX (accumulator)** -> used for arithmetic instructions and storing the **return value** of functions.
  - **ebx (base)** -> used as a base pointer for data access.
  - **ecx (counter)** -> used for loops and iterative operations.
  - **edx (data)** -> used for I/O operations and works with **eax** during multiplication/division.
  - **esi/edi (source/dest index)** -> used for memory array copying operations.
  - **esp (stack pointer)** -> always points to the top of the stack
  - **ebp (base pointer)** -> used as a reference point for the current stack frame, allowing access to function arguments and local variables.

### Common instructions (Intel Syntax):
  - `mov <dest>,<src>` -> copies data from source to destination 
  - `add <dest>,<src>` -> integer addition
  - `sub <dest>,<src>` -> integer subtraction
  - `cmp <op1>,<op2>`  -> compares the two operands (effectively performs `op1 - op2` to set flags, without storing the result)
  - `jmp <dest>` -> unconditional jump
  - `je <dest>` -> jump if equal
  - `jne <dest>` -> jump if not equal
  - `jg <dest>` -> jump if greater (signed)
  - `jng <dest>` -> jump if not greater (signed)
  - `jge <dest>` -> jump if greater or equal (signed)
  - `jl <dest>`  -> jump if less (signed)
  - `jle <dest>` -> jump if less or equal (signed)
  - `pop`        -> pops a value from the stack
  - `push`       -> pushes a value onto the stack

### Code analysis

We are calling the function **asm1** with the argument **0x3ef**.

```nasm
asm1:
	<+0>:	endbr32 
	<+4>:	push   ebp			                  ; saves ebp on stack
	<+5>:	mov    ebp,esp			              ; ebp = esp (creates a new stack frame)
	<+7>:	cmp    DWORD PTR [ebp+0x8],0x6e6      ; compare 0x3ef vs 0x6e6 (0x3ef - 0x6e6)
	<+14>:	jg     0x11d3 <asm1+38>		          ; is 0x3ef > 0x6e6? nope, do not jump.
	<+16>:	cmp    DWORD PTR [ebp+0x8],0x8	      ; compare 0x3ef vs 0x8 (0x3ef - 0x8)
	<+20>:	jne    0x11cb <asm1+30>		          ; is 0x3ef != 0x8? yes, jump to <+30>
	<+22>:	mov    eax,DWORD PTR [ebp+0x8]	      ; (skipped)
	<+25>:	add    eax,0x9			              ; (skipped)
	<+28>:	jmp    0x11ea <asm1+61>		          ; (skipped)
	<+30>:	mov    eax,DWORD PTR [ebp+0x8]        ; eax = 0x3ef
	<+33>:	sub    eax,0x9			              ; eax -= 0x9 => eax = 0x3e6
	<+36>:	jmp    0x11ea <asm1+61>	              ; jump to <+61>
	<+38>:	cmp    DWORD PTR [ebp+0x8],0x8f8      ; (skipped)
	<+45>:	jne    0x11e4 <asm1+55>		          ; (skipped)
	<+47>:	mov    eax,DWORD PTR [ebp+0x8]	      ; (skipped)
	<+50>:	sub    eax,0x9			              ; (skipped)
	<+53>:	jmp    0x11ea <asm1+61>		          ; (skipped)
	<+55>:	mov    eax,DWORD PTR [ebp+0x8]	      ; (skipped)
	<+58>:	add    eax,0x9			              ; (skipped)
	<+61>:	pop    ebp			                  ; restore old ebp
	<+62>:	ret    				                  ; return eax (0x3e6)
```

### How the stack looks for this code:

| address on stack | value at that address | comments
| :---: | :---: | :---: |
| [ebp + 8] | 0x000003ef | (first function argument) |
| [ebp + 4] | [ret addr] | return address |
| [ebp]	| [old ebp] | saved ebp (current base pointer) |

After the function call, eax will be `0x3e6`.
