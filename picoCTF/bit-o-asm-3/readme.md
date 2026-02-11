# Bit-O-Asm-3 - picoGym Exclusive

------

## Introduction
Bit-O-Asm-3 is a Reverse Engineering task. The goal is to figure out what is in the **eax** register.

Challenge description:

> Can you figure out what is in the eax register? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Download the assembly dump here.

------

## Solution by analysis of the assembly code

```nasm
<+0>:     endbr64 
<+4>:     push   rbp                           ; save rbp on stack
<+5>:     mov    rbp,rsp                       ; rbp = rsp (creates a new stack frame)
<+8>:     mov    DWORD PTR [rbp-0x14],edi      ; [rbp - 0x14] = edi
<+11>:    mov    QWORD PTR [rbp-0x20],rsi      ; [rbp - 0x20] = rsi
<+15>:    mov    DWORD PTR [rbp-0xc],0x9fe1a   ; [rbp - 0xc] = 0x9fe1a (local_var1)
<+22>:    mov    DWORD PTR [rbp-0x8],0x4       ; [rbp - 0x8] = 0x4 (local_var2)
<+29>:    mov    eax,DWORD PTR [rbp-0xc]       ; eax = local_var1 (0x9fe1a)
<+32>:    imul   eax,DWORD PTR [rbp-0x8]       ; eax = 0x9fe1a * 0x4 = 0x27F868
<+36>:    add    eax,0x1f5		               ; eax = eax + 0x1f5 = 0x27FA5D
<+41>:    mov    DWORD PTR [rbp-0x4],eax       ; [rbp - 0x4] = eax
<+44>:    mov    eax,DWORD PTR [rbp-0x4]       ; eax = [rbp - 0x4] = 0x27FA5D = D*1 + 5*16 + A*16^2 + F*16^3 + 7*16^4 + 2*16^5 = 2619997
<+47>:    pop    rbp                           ; restore rbp 
<+48>:    ret                                  ; return eax (2619997)
```
