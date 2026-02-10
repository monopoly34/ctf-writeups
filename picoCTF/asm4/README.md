# asm4 - picoCTF 2019

------

## Introduction
asm4 is a Reverse Engineering task. The goal is to see what does `asm4("picoCTF_88f44")` return.

Challenge description:

> What will asm4("picoCTF_88f44") return? Submit the flag as a hexadecimal value (starting with '0x'). NOTE: Your submission for this question will NOT be in the normal flag format. Source

------

## Solution by manual analysis of the assembly code

### If you haven't done so, I would highly recommend solving **[asm1](../../picoCTF/asm1)**, **[asm2](../../picoCTF/asm2)** and **[asm3](../../picoCTF/asm3)** first.

### How the stack looks for this code:

| address on stack | value at that address | comments
| :---: | :---: | :---: |
| [ebp + 8] | pointer to the memory address of the string 'picoCTF_88f44'  | (first function argument) |
| [ebp + 4] | [ret addr] | (return address) |
| [ebp]	| [old ebp] | saved ebp (current base pointer) |
| [ebp - 4] | [old ebx] | saved ebx on stack |
| [ebp - 8] | 0x1 | counter for the second loop, starts at 1 (i) |
| [ebp - 12] | 0x0 -> 0xd | counter for the first loop, end value acts as the string length (INDEX) |
| [ebp - 16] | 0x23a | this is the result returned at the end (RESULT) |

### Code analysis

We are calling the function **asm4** with the argument **asm4("picoCTF_88f44")**.

```nasm
asm4:
    <+0>:	endbr32 
  	<+4>:	push   ebp				                  ; save ebp on stack
  	<+5>:	mov    ebp,esp				              ; ebp = esp (creates a new stack frame)
  	<+7>:   push   ebx				                  ; save ebx on stack
  	<+8>:	sub    esp,0x10				              ; allocate 16 bytes on stack for local variables
  	<+11>:	mov    DWORD PTR [ebp-0x10],0x23a 	      ; RESULT = 0x23a 
  	<+18>:	mov    DWORD PTR [ebp-0xc],0x0		      ; INDEX = 0 (will hold string length)
  	<+25>:	jmp    0x11cc <asm4+31>			          ; jump to the first loop condition 
  	<+27>:	add    DWORD PTR [ebp-0xc],0x1		      ; INDEX++ 
    <+31>:	mov    edx,DWORD PTR [ebp-0xc]		      ; edx = INDEX
  	<+34>:	mov    eax,DWORD PTR [ebp+0x8]		      ; eax = base address of string "picoCTF_88f44"
  	<+37>:	add    eax,edx				              ; eax = base address + INDEX (address of current char)
  	<+39>:	movzx  eax,BYTE PTR [eax]		          ; read the char into eax
  	<+42>:	test   al,al				              ; check if the char is NULL ('\0')
  	<+44>:	jne    0x11c8 <asm4+27>			          ; jump if not equal, if not NULL, jump back to line <+27> to increment INDEX
  	<+46>:	mov    DWORD PTR [ebp-0x8],0x1		      ; i = 1 (loop counter for the second loop)
  	<+53>:	jmp    0x123b <asm4+142>		          ; jump to the second loop condition
  	<+55>:	mov    edx,DWORD PTR [ebp-0x8]		      ; edx = i
  	<+58>:	mov    eax,DWORD PTR [ebp+0x8]		      ; eax = base address of string "picoCTF_88f44"
  	<+61>:	add    eax,edx				              ; eax = address of s[i]
  	<+63>:	movzx  eax,BYTE PTR [eax]		          ; read s[i]
  	<+66>:	movsx  edx,al				              ; edx = s[i] (sx -> sign extended)
  	<+69>:	mov    eax,DWORD PTR [ebp-0x8]		      ; eax = i
  	<+72>:	lea    ecx,[eax-0x1]			          ; ecx = i - 1
  	<+75>:	mov    eax,DWORD PTR [ebp+0x8]		      ; eax = base address of string "picoCTF_88f44"
  	<+78>:	add    eax,ecx				              ; eax = address of s[i-1]
  	<+80>:	movzx  eax,BYTE PTR [eax]		          ; read s[i-1]
  	<+83>:	movsx  eax,al				              ; eax = s[i-1] (sign extended)
  	<+86>:	sub    edx,eax				              ; edx = s[i] - s[i-1]
  	<+88>:	mov    eax,edx				              ; eax = edx
  	<+90>:	mov    edx,eax				              ; edx = eax
  	<+92>:	mov    eax,DWORD PTR [ebp-0x10]		      ; eax = RESULT
  	<+95>:	lea    ebx,[edx+eax*1]			          ; ebx = edx + RESULT
  	<+98>:	mov    eax,DWORD PTR [ebp-0x8]	  	      ; eax = i
  	<+101>:	lea    edx,[eax+0x1]			          ; edx = i + 1
  	<+104>:	mov    eax,DWORD PTR [ebp+0x8]		      ; eax = base address of string "picoCTF_88f44"
  	<+107>:	add    eax,edx				              ; eax = address of s[i+1]
  	<+109>:	movzx  eax,BYTE PTR [eax]		          ; read s[i+1]
  	<+112>:	movsx  edx,al				              ; edx = s[i+1]
  	<+115>:	mov    ecx,DWORD PTR [ebp-0x8]		      ; ecx = i
  	<+118>:	mov    eax,DWORD PTR [ebp+0x8]		      ; eax = base address of string "picoCTF_88f44"
  	<+121>:	add    eax,ecx				              ; eax = address of s[i]
  	<+123>:	movzx  eax,BYTE PTR [eax]		          ; read s[i]
  	<+126>:	movsx  eax,al				              ; eax = s[i]
  	<+129>:	sub    edx,eax				              ; edx = s[i+1] - s[i]
  	<+131>:	mov    eax,edx				              ; eax = s[i + 1] - s[i]
  	<+133>:	add    eax,ebx				              ; eax = s[i+1] - s[i] + (s[i] - s[i-1] + RESULT)
  	<+135>:	mov    DWORD PTR [ebp-0x10],eax		      ; RESULT = eax
  	<+138>:	add    DWORD PTR [ebp-0x8],0x1		      ; i++
  	<+142>:	mov    eax,DWORD PTR [ebp-0xc]		      ; eax = INDEX
  	<+145>:	sub    eax,0x1				              ; eax = INDEX - 1
  	<+148>:	cmp    DWORD PTR [ebp-0x8],eax		      ; i - (INDEX - 1)
  	<+151>:	jl     0x11e4 <asm4+55>			          ; jump if lower (jump to offset <+55> if i < (INDEX -1))
  	<+153>:	mov    eax,DWORD PTR [ebp-0x10]		      ; eax = RESULT (this will be the return value) 
  	<+156>:	add    esp,0x10				              ; free local variables from stack
  	<+159>:	pop    ebx				                  ; restore old ebx
  	<+160>:	pop    ebp				                  ; restore old ebp
  	<+161>:	ret    					                  ; return eax
```

### Since I am too lazy to go through all the iterations manually, I wrote a Python script to simulate the logic.

```python
string = "picoCTF_88f44"
result = 0x23a
length = len(string)

for i in range(1,length-1):
    diff1 = ord(string[i]) - ord(string[i-1])
    diff2 = ord(string[i+1]) - ord(string[i])
    result = result + diff1 + diff2

print(f"flag: {hex(result)}")
```
