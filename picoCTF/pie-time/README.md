# PIE TIME - picoCTF 2025

------

## Introduction
PIE TIME is a Binary Exploitation task. We are given a source code, a server to connect to and a binary, that we can use to figure out the flag.

Challenge description:

> Can you try to get the flag? Beware we have PIE! Connect to the program with netcat:
$ nc rescued-float.picoctf.net 51746
The program's source code can be downloaded here. The binary can be downloaded here.

------

## How to find the flag

1. First, I connected to the server using `nc rescued-float.picoctf.net 51746` to see what information can it give me. The first thing I noticed, the server gives me the address of main and asks me for an address to jump to.

```bash
Address of main: 0x5aba8ab0333d
Enter the address to jump to, ex => 0x12345:
```

2. Next, I opened the source code using **vim** and searched for any clues that would help me to find the flag.

```C
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

void segfault_handler() {
  printf("Segfault Occurred, incorrect address.\n");
  exit(0);
}

int win() {
  FILE *fptr;
  char c;

  printf("You won!\n");
  // Open file
  fptr = fopen("flag.txt", "r");
  if (fptr == NULL)
  {
      printf("Cannot open file.\n");
      exit(0);
  }

  // Read contents from file
  c = fgetc(fptr);
  while (c != EOF)
  {
      printf ("%c", c);
      c = fgetc(fptr);
  }

  printf("\n");
  fclose(fptr);
}

int main() {
  signal(SIGSEGV, segfault_handler);
  setvbuf(stdout, NULL, _IONBF, 0); // _IONBF = Unbuffered

  printf("Address of main: %p\n", &main);

  unsigned long val;
  printf("Enter the address to jump to, ex => 0x12345: ");
  scanf("%lx", &val);
  printf("Your input: %lx\n", val);

  void (*foo)(void) = (void (*)())val;
  foo();
} 
```

There were two functions that caught my eye, the **int main()** and the **int win()**. We know the address of the main, we have to figure out the address of the win. We can use the binary for that.

3. I then used the `nm ./vuln | grep "win"` and `nm ./vuln | grep "main"` commands to see the distance in bytes between these two functions. After subtracting the win address from the main address I was left with the number **0x96**, which is exactly the distance in bytes that we are looking for. We connected again to the server, subtracted the distance from the address of main to find the address of **win**. I then jumped to the address I calculated earlier and found the flag: picoCTF{-}
