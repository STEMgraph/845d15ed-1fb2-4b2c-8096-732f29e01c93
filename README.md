<!---
{
  "id": "845d15ed-1fb2-4b2c-8096-732f29e01c93",
  "depends_on": ["1394df5e-2528-4670-9398-208071d70c1b"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-04-01",
  "keywords": ["fscanf", "streams", "C", "input"]
}
--->

# From `scanf` to `fscanf`: Reading Input from Files

## 1) Introduction

In the [previous exercise](https://github.com/STEMgraph/1394df5e-2528-4670-9398-208071d70c1b), we explored `scanf`, which allows us to read formatted input from the terminal (i.e., from `stdin`). In this follow-up, we generalize this idea: just like `printf` has `fprintf`, there’s `fscanf` for reading from **arbitrary input streams**, such as files.

Let’s first understand the low-level idea by looking at how we might read from a file using Assembly.

```asm
01  section .data
02      filename db '/home/user/input.txt', 0
03
04  section .bss
05      buffer resb 64
06
07  section .text
08      global _start
09
10  _start:
11      mov     rax, 2              ; syscall: open
12      lea     rdi, [rel filename] ; pointer to filename
13      mov     rsi, 0              ; flags = O_RDONLY
14      syscall
15      mov     r12, rax            ; save file descriptor
16
17      mov     rax, 0              ; syscall: read
18      mov     rdi, r12            ; fd from open
19      lea     rsi, [rel buffer]
20      mov     rdx, 64             ; number of bytes
21      syscall
22
23      mov     rax, 3              ; syscall: close
24      mov     rdi, r12
25      syscall
26
27      mov     rax, 60             ; exit
28      xor     rdi, rdi
29      syscall
```

This reads up to 64 bytes from a file and places them into a buffer.

Now let’s perform the same operation in C using `fscanf`. We’ll open a file and read formatted text from it:

```c
#include <stdio.h>

int main(void) {
    FILE *file = fopen("./input.txt", "r");
    if (!file) {
        perror("Could not open file");
        return 1;
    }

    char name[32];
    int age;

    fscanf(file, "%31s %d", name, &age);
    printf("Name: %s, Age: %d\n", name, age);

    fclose(file);
    return 0;
}
```
This assumes a file like `./assets/input.txt`.
`fscanf` works exactly like `scanf`, except it takes an additional `FILE*` stream as its first argument. It allows us to read structured input from any file, not just from the terminal.

## 2) Tasks

1. **Try fscanf**: Create a file called `input.txt` with some example data (e.g., name and age). Write a program that reads and prints it using `fscanf`.
2. **Compare with scanf**: Try reading the same data with `scanf` using input redirection: `./a.out < input.txt`. What’s the difference?
3. **Use fgets + sscanf**: Read the documentation of `sscanf` on [the scanf page of cppreference.com](https://en.cppreference.com/w/c/io/fscanf). Instead of `fscanf`, use `fgets` to read a full line, then parse it using `sscanf`.


## 3) Questions

1. What is the difference between `scanf` and `fscanf`, and when would you use each?

2. How does `fscanf` generalize the behavior of `scanf`?

3. Why is it important to open files in the correct mode (`"r"`, `"w"`, etc.)?

4. What happens if the format string in `fscanf` doesn’t match the file contents?

## 4) Advice

Understanding how to read from different sources is crucial for systems programming. `fscanf` gives you the power to work with structured input from files in the same way `scanf` works with the terminal. Always validate file opening, watch for format mismatches, and consider `fgets` + `sscanf` when you need more control over parsing.

