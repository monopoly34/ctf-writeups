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
<+4>:     push   rbp                         ; save rbp on stack
<+5>:     mov    rbp,rsp                     ; rbp = rsp
<+8>:     mov    DWORD PTR [rbp-0x14],edi    ; save at the address [rbp - 0x14] the value of edi
<+11>:    mov    QWORD PTR [rbp-0x20],rsi    ; save at the address [rbp - 0x20] the value of rsi
<+15>:    mov    DWORD PTR [rbp-0x4],0x9fe1a ; [rbp - 0x4] = 0x9fe1a (local_var)
<+22>:    cmp    DWORD PTR [rbp-0x4],0x2710  ; compares local_var with the value 0x2710 (0x9fe1a - 0x2710)
<+29>:    jle    0x55555555514e <main+37>    ; jump if lower or equal, since 0x9fe1a is bigger than 0x2710, the program does not jump
<+31>:    sub    DWORD PTR [rbp-0x4],0x65    ; local_var - 0x65 = 0x9FDB5
<+35>:    jmp    0x555555555152 <main+41>    ; jumps to offset <+41>
<+37>:    add    DWORD PTR [rbp-0x4],0x65    ; (skipped)
<+41>:    mov    eax,DWORD PTR [rbp-0x4]     ; eax = local_var (eax = 0x9FDB5 which will be 654773 in decimal)	
<+44>:    pop    rbp                         ; restore rbp 
<+45>:    ret                                ; return eax (654773)
```
