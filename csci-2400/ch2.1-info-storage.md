# Information Storage

Rather than accessing individual bits, most computes use blocks of eight bits, or *bytes*. 
* A machine-level program views memory as a very large array of bytes, referred to as *virtual memory*.
* Every byte of memory is identified by a unique number, known as its *address*.
* The set of all possible addresses is known as the *virtual address space*. 

#### Hexadecimal Notation

A single byte consists of 8 bits. In binary notation, its value ranges from $00000000_2$ to $11111111_2$. When viewed as a decimal integer, its values ranges from $0_10$ to $255_10$. 

To combat the lack of bit pattern description with binary notation -- that is too verbose -- and decimal notation -- that is too tedious to convert -- we write bit patterns as base-16, or *hexadecimal* numbers. 

<img width="533" height="146" alt="image" src="https://github.com/user-attachments/assets/6f441cee-c8cd-4a86-8772-533395adcfc4" />

For example, given the number $0x173A4C$, the binary representation would be: $0001$ (1) $0111$ (7) $0011$ (3) $1010$ (A) $0100$ (4) $1100$ (C). 

<img width="410" height="47" alt="image" src="https://github.com/user-attachments/assets/e1079960-9f3e-4c18-ab83-3d40baf22d69" />

==FINISH==
==FINISH==
==FINISH==
==FINISH==
==FINISH==

#### Words

Every computer has a *word size*, indicating the nominal size of integer and pointer data. This, in turn, represents the maximum size of the virtual address space. That is, for a machine with a $w$-bit word size, the virtual addresses can range from $0$ to $2^{w}-1$. 

The difference between a 32-bit computer and a 64-bit computer is a virtual address space of 4 GB and a virtual addressed space of 16 TB. 

#### Data Sizes

<img width="396" height="264" alt="image" src="https://github.com/user-attachments/assets/15395ad8-c7ca-4140-b3cf-72e5fb37485f" />

The declaration of a point is denoted by the `*` symbol. So, `char *{variable name}` is a pointer to an object of type `char` described by the name `{variable name}`. 

#### Addressing and Byte Ordering

.

#### Representing Strings

A string in C is encoded by an array of characters terminated by the null. Each character is represented by some standard encoding, with the most common being the ASCII character code. 

#### Representing Code

Consider the following C function:

```c
int sum(int x, int y) {
  return x+y;
}
```

The machine code generated has the following byte representations dependent on your machine:

<img width="516" height="131" alt="image" src="https://github.com/user-attachments/assets/205cc325-f974-415c-a319-1d2813692af4" />

#### Introduction to Boolean Algebra

| Operation | Result |
| --- | --- |
| $a$ | $[01101001]$ |
| $b$ | $[01010101]$ |
| $\neg a$ | $[10010110]$ |
| $\neg b$ | $[10101010]$ |
| $a$ & $b$ | $[01000001]$ |
| $a \| b$ | $[01111101]$ |
| $a$ $\textasciicircum$ $b$ | $[00111100]$ |

#### Bit-Level Operations in C

<img width="497" height="108" alt="image" src="https://github.com/user-attachments/assets/42a7fdc6-7d5f-4328-805f-25b14f165f86" />

#### Logical Operations in C

<img width="157" height="123" alt="image" src="https://github.com/user-attachments/assets/2b54e455-2379-4549-b214-afe4750f6d47" />
