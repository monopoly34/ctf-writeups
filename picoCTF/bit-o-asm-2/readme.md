# Bit-O-Asm-2 - picoGym Exclusive

------

## Introduction
Bit-O-Asm-2 is a Reverse Engineering task. The goal is to figure out what is in the **eax** register.

Challenge description:

> Can you figure out what is in the eax register? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Download the assembly dump here.

------

## Solution by analysis of the assembly code

```nasm
<+0>:     endbr64 	
<+4>:     push   rbp				            ; save rbp on stack		
<+5>:     mov    rbp,rsp			            ; rbp = rsp (creates a new stack frame)
<+8>:     mov    DWORD PTR [rbp-0x14],edi	    ; save at the address [rbp - 0x14] the value of edi
<+11>:    mov    QWORD PTR [rbp-0x20],rsi	    ; [rbp - 0x20] = rsi
<+15>:    mov    DWORD PTR [rbp-0x4],0x9fe1a	; [rbp - 0x4] = 0x9fe1a (local_var)
<+22>:    mov    eax,DWORD PTR [rbp-0x4]	    ; eax = 0x9fe1a (eax = 10*1 + 1*16 + 14*16^2 + 15*16^3 + 9*16^4 = 654874)
<+25>:    pop    rbp                            ; restore rbp
<+26>:    ret                                   ; return eax (654874)
```
