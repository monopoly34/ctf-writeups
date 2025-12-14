# asm2 - picoCTF 2019

------

## Introduction
asm2 is a Reverse Engineering task. The goal is to see what does `asm2(0x6,0x21)` returns.

Challenge description:

> What does asm2(0x6,0x21) return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. Source

------

## Solution by manual analysis of the assembly code

### If you are unfamiliar with x86 syntax or you need a little refresher, I would highly recommend trying **[asm1](../../picoCTF/asm1)** first.

### How the stack looks for this code:

| address on stack | value at that address | comments
| :---: | :---: | :---: |
| [ebp + 12] | 0x00000021 | (second function argument) |
| [ebp + 8] | 0x00000006 | (first function argument) |
| [ebp + 4] | [ret addr] | (return address) |
| [ebp]	| [old ebp] | saved ebp (current base pointer) |
| [ebp - 4] | local-variable-1 | (first local variable) |
| [ebp - 8] | local-variable-2 | (second local variable) |

### Code analysis

We are calling the function **asm2** with the arguments **0x6** and **0x21**.

```nasm
asm2:
	<+0>:	endbr32 
	<+4>:	push   ebp   			            ; saves ebp on stack
  	<+5>:	mov    ebp,esp		      	        ; ebp = esp (creates new stack frame)
	<+7>:	sub    esp,0x10			            ; allocate space for local variables
	<+10>:	mov    eax,DWORD PTR [ebp+0xc]	    ; eax = 0x21 (arg 2)
	<+13>:	mov    DWORD PTR [ebp-0x4],eax	    ; local-variable-1 = 0x21 
	<+16>:	mov    eax,DWORD PTR [ebp+0x8]	    ; eax = 0x6 (arg 1)
	<+19>:	mov    DWORD PTR [ebp-0x8],eax	    ; local-variable-2 = 0x6
	<+22>:	jmp    0x11d0 <asm2+35>		        ; jump to the while loop condition check
	<+24>:	add    DWORD PTR [ebp-0x4],0x1	    ; local-variable-1 += 0x1
	<+28>:	add    DWORD PTR [ebp-0x8],0x9f	    ; local-variable-2 += 0x9f
	<+35>:	cmp    DWORD PTR [ebp-0x8],0x2d12   ; compare local-variable-2 vs 0x2d12 (while loop condition)
	<+42>:	jle    0x11c5 <asm2+24>	            ; local-variable-2 <= 0x2d12? then jump to <+24>
	<+44>:	mov    eax,DWORD PTR [ebp-0x4]	    ; eax = local-variable-1		
	<+47>:	leave  				                ; clean up stack
	<+48>:	ret    				                ; return eax = local-variable-1
```

### Since calculating the loop manually is tedious, I wrote a short Python script to simulate the logic.

```python
var1 = 0x21
var2 = 0x6

while (var2 <= 0x2d12):
    var1 += 1
    var2 += 0x9f

print(hex(var1))    
```

After the function call, eax will be `0x6a`.

