# DISKO 1 - picoGym Exclusive

-----

## Introduction
DISKO 1 is a Forensics task. The goal is to find the flag hidden in a disk image.

Challenge description:

> Can you find the flag in this disk image? Download the disk image here.

-----

## My approach to finding the flag

1. I tried to see what was inside the disk image by using `strings`.

```bash
strings disko-1.dd
```
I noticed that the file was really huge, so it would be hard to search for the flag manually.

2. Next, I tried use `grep`, but I was given the following error "`grep: disko-1.dd: binary file matches`".

```bash
grep -i "picoCTF" disko-1.dd
```
3. In the end, I tried to combine `strings` and `grep`, hoping this time I would find the flag.

```bash
strings -n 10 disko-1.dd | grep -i "picoCTF"
```
This command will search for `strings` at least 10 characters long `-n 10`, then pipe the output to `grep`. The flag: picoCTF{1t5_ju5t_4_5tr1n9_be6031da}
