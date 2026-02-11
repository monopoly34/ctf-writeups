# Bit-O-Asm-1 - picoGym Exclusive

------

## Introduction
Bit-O-Asm-1 is a Reverse Engineering task. The goal is to figure out what is in the **eax** register.

Challenge description:

> Can you figure out what is in the eax register? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Download the assembly dump here.

------

## Solution by analysis of the assembly code

```nasm
<+0>:     endbr64 
<+4>:     push   rbp			            ; save rbp on stack
<+5>:     mov    rbp,rsp	      	        ; rbp = rsp (create a new stack frame)
<+8>:     mov    DWORD PTR [rbp-0x4],edi    ; save at the address [rbp - 0x4] the value of edi 
<+11>:    mov    QWORD PTR [rbp-0x10],rsi   ; [rbp - 0x10] = rsi
<+15>:    mov    eax,0x30		            ; eax = 0x30 = 0*1 + 3*16 = 48 
<+20>:    pop    rbp                        ; restore rbp
<+21>:    ret                               ; return eax (48)
```
