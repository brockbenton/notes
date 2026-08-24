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

<img width="401" height="293" alt="image" src="https://github.com/user-attachments/assets/bdf64a89-7e35-409b-8bf8-3c8d4fb83a4f" />

* *Buses.* Electrical conduits that carry bytes of information back and forth components. They are typically designed to transfer fixed-sized chunks of bytes known as *words*.
* *I/O Devices.*
* *Main Memory.* A temporary storage device that holds both a program and the data it manipulates while the processor is executing the program. Physically, this is a collection of *dynamic random access memory* (DRAM).
* *Processor.* The *central processing unit* (CPU) is the engine that interprets instructions stored in main memory. At its core is a word-sized storage device (or *register*) called the *program counter*. 

#### Running the `hello` program

1. As we type the characters "`./hello`" at the keyboard, the shell program reads each one into a register, and then stores it in memory.
2. When we hit the `enter` key, the shell knows that we have finished and then loads the executable `hello` file by executing a sequence of instructions that copy the code and data in the `hello` object file from disk to main memory.
3. Using a technique known as *direct memory access* (DMA), the data travels directly from disk to main memory, without passing through the processor.
4. Once the code and data in the `hello` object file are loaded into memory, the processor begins executing the machine-language instructions in the `hello` program's `main` routine. 

## Caches Matter

*Cache memories* are temporary staging areas for information that the processor is likely to need in the near future. 
* An *L1 cache* on the processor chip holds tens of thousands of bytes and can be accessed nearly as fast as the register file.
* A larger *L2 cache* holds hundreds of thousands to millions of bytes but might be up to 5 times as slow as the L1 cache.
* Most modern systems have a *L3 cache*. 

## Storage Devices Form a Hierarchy

<img width="510" height="301" alt="image" src="https://github.com/user-attachments/assets/6c7e89e5-4de9-40ac-a1e3-965d1af76f4f" />

## The Operating System Manages the Hardware

An operating system is a type of middle-layer that sits between the application layer and the hardware layer. The operating system has two primary purposes:
1. To protect the hardware from misuse by runaway applications.
2. To provide applicatiopns with simple and uniform mechanism for manipulating complicated and often wildly different low-level hardware devices. 

#### Processes

A *process* is the operating system's abstraction for a running program. Multiple processes can run *concurrently* (instructions of one are interleaved with the instructions of another), and each process appears to have exclusive use of the hardware. This mechanism is described as *context switching*. 

Using the `hello` example, here is the process flow: 
1. Shell is running alone.
2. When we run the `hello` program, the shell carries out our request by invoking a *system call* that passes control to the operating system.
3. The operating system saves the shell's context, creates a new `hello` process and its context, and then passes control to the new `hello` process.
4. After `hello` terminates, the operating system restores the context of the shell.

<img width="446" height="172" alt="image" src="https://github.com/user-attachments/assets/e19dfc4c-384c-44b8-b853-7a44ca2da295" />

#### Threads

A process can consist of multiple execution units, called *threads*, each running in the context of the process and sharing the same code and global data. It is easier to share data between threads than it is between processes, so multi-threading is one way to make programs run faster when multiple processors are available. 

#### Virtual Memory

*Virtual memory* is an abstraction that provides each process with the illusion that it has exclusive use of the main memory. 

#### Files

## Systems Communicate with Other Systems Using Networks
