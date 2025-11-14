# Corrupted file - picoMini by CMU-Africa

-----

## Introduction
Corrupted file is a Forensics task. The goal is to dig into a corrupted file's hex and modify it so you can recover the information in the original file.

Challenge description:

> This file seems broken... or is it? Maybe a couple of bytes could make all the difference. Can you figure out how to bring it back to life? Download the file here.

-----

## My approach to finding the flag

1. First thing I checked was to see if I could find anything useful inside the file. So, I used the command `cat` to see what's inside.

```bash
cat file
```
2. At first, all I saw was random letters, weird characters and numbers, so I thought, maybe the characters were the problem. So, I used a command to try and get rid of those characters.

```bash
tr -d -c '\11\12\15\40-\176' < file > newfile
```
This command will delete `-d` anything not in the specified set `-c`. The set contains `\11`,`\12`,`\15`, which are tab, LF and CR, and the everything from `\40` to `\176`, which are all printable characters. After opening the `newfile`, there was nothing changed. So, I backtracked and tried to do something else.

3. I tried to look again inside the file, by using the `strings` command. This time I was lucky and I came across an interesting string "`JFIF`", which led me to my next step, to try and see the header of the file by using `od(octal dump)`, a command used for inspecting the first few bytes (the header) of a file.

```bash
od -bc file | head
```
4. After a few minutes of reading online, I found that `JFIF` is short for `JPEG File Interchange Format`, which is now rarely used. So, I figured that I would need to modify the hex to make it a `JPEG`. I started by downloading a hex editor, I used `ghex`. I opened the file's hex and changed from `JFIF` to `JPEG`, and I also had to change the marker hex code values to `FF D8 FF`. After these changes, I saved the file and added the `.jpeg` format, upon opening the jpeg file I noticed the flag written on the picture. So, I used `tesseract-ocr` to extract the flag: picoCTF{r3st0r1ng_th3_by73s_efd8c6c0}

```bash
tesseract file.jpeg flag
```
 
