# A Tour of Computer Systems

The sections below will take the following example,
```c
#include <stdio.h>

int main()
{
  printf("hello, world\n");
}
```

## Information Is Bits + Context

The `hello` program above beings life as a *source program* that it is saved in a text file called `hello.c`. The source program is a sequence of bits, each with a value of 0 or 1, organized in 8-bit chunks called *bytes*.

Each byte has an integer value as defined by ASCII representation. For example, the first byte has the integer value 35, which corresponds to the character `#`. 

## Programs Are Translated by Other Programs into Different Forms

In order to run `hello.c`, the individual C statements must be translated by other programs into a sequence of low-level *machine-language* instructions. These instructions are then packaged in a form called an *executable object program*. 

On a Unix system, the translation from source file to object file is performed by a *compiler driver*, 

<img width="549" height="114" alt="image" src="https://github.com/user-attachments/assets/18fb9b70-f946-4c53-bdcc-c0f8304f0b24" />

Here, the `GCC` compiler translates this through four phases:
1. *Preprocessing phase.* The preprocesser (`cpp`) modifies the original C program according to directives that begin with the `#` character. In the `hello` example, this means inserting the contents of the system header file `stdio.h`. The result is another program, typically with the `.i` suffix.
2. *Compilation phase.* The compiler (`cc1`) translates the text file `hello.i` into the text file `hello.s`, which contains an *assembly-language program*.
3. *Assembly phase.* The assembler (`as`) translates `hello.s` into machine-language instructions, packages them in a form known as a *relocatable object program*, and stores the result in the object file `hello.o`. This file is a binary file whose bytes encode machine language instructions rather than characters, appearing as gibberish.
4. *Linking phase.* Notice that the `hello` program calls the `printf` function, which is part of the *standard C library*. The `printf` function resides in a separate precompiled object file called `printf.o`. The linked (`ld`) handles the merging of the `hello` object file, and the "external" object files.

## It Pays to Understand How Compilation Systems Work

Some reasons include:
* *Optimizing program performance.* Is a `switch` statement always more efficient than a sequence of `if-else` statements?
* *Understanding link-time errors.* What is the difference between static and global variables?
* *Avoiding security holes.* Why should you restrict the quantity and forms of data programs accept?

## Processors Read and Interpret Instructions Stored in Memory

Now, the `hello.c` source program has been translated by the compilation system into an executable object file called `hello` that is stored on disk. To run this through the *shell*, 

```
unix> ./hello
hello, world
unix>
```

The shell is a command-line interpreter. 

#### Hardware Organization of a System 

#### Running the `hello` program

## Caches Matter
## Storage Devices Form a Hierarchy

<img width="510" height="301" alt="image" src="https://github.com/user-attachments/assets/6c7e89e5-4de9-40ac-a1e3-965d1af76f4f" />

## The Operating System Manages the Hardware


## Systems Communicate with Other Systems Using Networks
