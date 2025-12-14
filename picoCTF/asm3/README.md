# asm3 - picoCTF 2019

------

## Introduction
asm3 is a Reverse Engineering task. The goal is to see what does `asm3(0xb75a8f13,0xe1860bd7,0xc8e62f81)` returns.

Challenge description:

> What does asm3(0xb75a8f13,0xe1860bd7,0xc8e62f81) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. Source

------

## Solution by manual analysis of the assembly code

### If you are unfamiliar with x86 syntax or you need a little refresher, I would highly recommend trying **[asm1](../../picoCTF/asm1)** and **[asm2](../../picoCTF/asm2)** first.

Before analyzing the source code, it's crucial to understand how x86 registers can be accessed partially.
A 32-bit register like **eax** contains a 16-bit part called **ax**.
The 16-bit **ax** is further divided into two 8-bit registers:
  + **ah** (a-high or high byte of ax)
  + **al** (a-low or low byte of ax)

Modifying `ah` or `al` directly affects the value of `ax` (and consequently `eax` and `rax`), but allows for byte-level manipulation.

### How the stack looks for this code:

| address on stack | value at that address | value (little endian bytes) | comments
| :---: | :---: | :---: | :---: |
| [ebp + 16] | 0xc8e62f81 | `81 2f e6 c8` | (third function argument ) |
| [ebp + 12] | 0xe1860bd7 | `d7 0b 86 e1` | (second function argument) |
| [ebp + 8] | 0xb75a8f13 |  `13 8f 5a b7` | (first function argument) |
| [ebp + 4] | [ret addr] | - | (return address) |
| [ebp]	| [old ebp] |  - | saved ebp (current base pointer) |

### Code analysis

We are calling the function **asm3** with the arguments **0xc8e62f81**, **0xe1860bd7** and **0xb75a8f13**.

```nasm
asm3:
	<+0>:	endbr32 
	<+4>:	push   ebp		               ; saves ebp on stack
	<+5>:	mov    ebp,esp		           ; ebp = esp (creates a new stack frame)
	<+7>:	xor    eax,eax	               ; eax = 0 (anything XORed with itself is 0)
	<+9>:	mov    ah,BYTE PTR [ebp+0x9]   ; [ebp+0x8] is 0xb75a8f13
                                           ; +0x9 points to the second byte of the first argument
                                           ; bytes: 13 [8f] 5a b7
                                           ; ah = 0x8f => ax = 0x8f00
	<+12>:	shl    ax,0x10		           ; shl <op>,<quantity> -> shift logical left
                                           ; shift ax left by 16 bits
                                           ; since ax is 16-bit, all bits are shifted out
                                           ; ax = 0x0000
	<+16>:	sub    al,BYTE PTR [ebp+0xf]   ; [ebp+0xc] is 0xe1860bd7
                                           ; +0xf points to the third byte of the second argument
                                           ; bytes: d7 0b 86 [e1]
                                           ; al = 0x00 - 0xe1 = 0x1f (because of the overflow it wraps around)
                                           ; ax = 0x001f
	<+19>:	add    ah,BYTE PTR [ebp+0xd]   ; [ebp+0xc] is 0xe1860bd7
                                           ; +0xd points to the second byte of the second argument
                                           ; bytes: d7 [0b] 86 e1
                                           ; ah = 0x00 + 0x0b = 0x0b 
                                           ; ax = 0x0b1f
	<+22>:	xor    ax,WORD PTR [ebp+0x12]  ; [ebp+0x10] is 0xc8e62f81
                                           ; since WORD is 2 bytes, +0x12 points to the  last 2 bytes (offset 2 & 3)
                                           ; bytes: 81 2f [e6 c8]
                                           ; WORD is 0xc8e6
                                           ; ax = 0x0b1f xor 0xc8e6
	<+26>:	nop                            ; an assembly command that does literally nothing
	<+27>:	pop    ebp                     ; restore old ebp
	<+28>:	ret                            ; return eax (0xc3f9)
```

After the function call, eax will be `0xc3f9`.


