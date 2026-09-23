# Assembly

The following notes are stripped from chapters 3.2, 3.4, 3.5, 3.6, 3.7, 3.8, and 3.11 of *Computer Systems: A Programmer's Perspective* by Bryant and O'Hallaron. This is a simple introductory to coding in assembly, particularly relating to the information needed to solve the second lab of CSCI 2400 (Bomb Lab). 

### Registers & Data Movement

A register is a tiny storage slot built directly into the CPU chip. It's the only place the CPU can actually do work. So if the CPU wants to add two numbers, it must pull them into registers (let's say from RAM), do the math there, and then if needed, write the result back out (to RAM). 

| Register     | Conventional role             |
|--------------|--------------------------------|
| %rax         | Return value                   |
| %rbx         | Callee-saved                   |
| %rcx         | Arg 4                          |
| %rdx         | Arg 3                          |
| %rsi         | Arg 2                          |
| %rdi         | Arg 1                          |
| %rbp         | Frame pointer (often omitted)  |
| %rsp         | Stack pointer                  |
| %r8–%r9      | Args 5–6                       |
| %r10–%r11    | Caller-saved scratch           |
| %r12–%r15    | Callee-saved                   |

Operand forms, 

| Form            | Meaning                                       |
|-----------------|------------------------------------------------|
| $0x1f           | Immediate (literal constant)                    |
| %rax            | Register                                        |
| (%rax)          | Memory at address held in %rax                  |
| 8(%rax)         | Memory at %rax + 8                              |
| (%rax,%rcx,4)   | Memory at %rax + %rcx $\times$ 4        |

Moving data,

| Instruction | Effect                                                           |
|-------------|-------------------------------------------------------------------|
| mov S, D    | D ← S (copy a value from source S to destination D). Suffix sets size: movb/movw/movl/movq (byte/word/dword/qword) |
| movz S, D   | Move with zero-extension into a larger destination (extra bits fill with 0s)               |
| movs S, D   | Move with sign-extension into a larger destination (extra bits fill with sign)               |
| push S      | %rsp ← %rsp−8; then M[%rsp] ← S (grows the stack downward by subtracting 8 from %rsp and then writing the value there)                                  |
| pop D       | D ← M[%rsp]; then %rsp ← %rsp+8 (shrinks the stack upwards by reading value then adding 8 back to %rsp to release that space)      |

### Arithmetic & $\text{lea}$

`lea` is the load effective address. This is used to compute an address, but it never reads memory. 

 | Instruction           | Effect                                                    |
  |------------------------|-----------------------------------------------------------|
  | lea S, D               | D ← &S — address computation only, no memory read          |
  | add S, D               | D ← D + S                                                  |
  | sub S, D                | D ← D − S                                                  |
  | imul S, D               | D ← D × S                                                  |
  | xor / or / and S, D     | D ← D ^ S  /  D \| S  /  D & S                              |
  | inc / dec / neg / not D | D+1 / D−1 / −D / ~D                                         |
  | sal/shl k, D            | D ← D << k (left shift)                                     |
  | sar k, D                | D ← D >> k, arithmetic (sign-preserving)                    |
  | shr k, D                | D ← D >> k, logical (zero-fill)                             |

### Condition Codes & Comparisons

Both instructions do a calculation and immediately throw the result away.
The only thing they keep is the flags (ZF/SF/CF/OF) that calculation produced.
They exist purely to feed the next instruction, which will be a conditional jump.

| Flag | Set when…                              |
|------|------------------------------------------|
| ZF   | Result was zero                          |
| SF   | Result was negative                      |
| CF   | Unsigned carry/borrow occurred            |
| OF   | Signed (two's-complement) overflow occurred |

**cmp S2, S1**
- Computes S1 − S2.
- It only updates the flags based on what that subtraction would have given.
- Read the operands backwards from how they're written: `cmp $5, %eax` means
  "compute %eax − 5"

 An example: %eax = 5 → cmp $5,%eax → 5 − 5 = 0 → ZF gets set to 1 (result was zero)

- The jump instruction right after reads those flags to decide what "equal,"
  "greater," "less," etc. actually means for this specific comparison.

**test S2, S1**
- Computes S1 & S2 (bitwise AND).
- The near-universal use is `test %eax,%eax`. ANDing a register with itself
  doesn't change anything mathematically, but it still sets ZF (if the value
  was 0) and SF (if the top bit was set, i.e. negative as a signed number).
  So `test %eax,%eax` is just a cheap "what's the sign/zero-ness of %eax?"
  check with only one operand needed, instead of writing `cmp $0,%eax`.

### Jump Instructions

| Signed | Unsigned | Condition | Meaning         |
|--------|----------|-----------|------------------|
| je     | je       | ZF        | Equal / zero     |
| jne    | jne      | ~ZF       | Not equal / not zero |
| js     | —        | SF        | Negative         |
| jns    | —        | ~SF       | Nonnegative      |
| jg     | ja       | (combo)   | Greater than     |
| jge    | jae      | (combo)   | Greater or equal |
| jl     | jb       | (combo)   | Less than        |
| jle    | jbe      | (combo)   | Less or equal    |

### Loop Patterns

Compiled loops (for/while/do-while) almost always reduce to the same shape:
a body of instructions, followed by a comparison, followed by a conditional
jump backward to an earlier address (that's what makes it a loop instead of
a straight line).

**C code:**
```c
int sum_array(int *arr, int n) {
    int sum = 0;
    int i = 0;
    while (i < n) {
        sum += arr[i];
        i++;
    }
    return sum;
}
```

**Compiled x86-64 (`gcc -O0`, args: %rdi = arr, %esi = n):**
```
sum_array:
    mov    $0x0,%eax            # sum = 0 (eax will hold the return value)
    mov    $0x0,%ecx            # i = 0
    jmp    L_check              # jump straight to the test, before running the body once

L_top:
    mov    (%rdi,%rcx,4),%edx   # edx = arr[i] so base %rdi + index %rcx * 4 bytes (the space taken up by an int)
    add    %edx,%eax            # sum += arr[i]
    add    $0x1,%ecx            # i++

L_check:
    cmp    %esi,%ecx            # compare i to n
    jl     L_top                # if i < n (signed less-than), jump back to L_top

    ret                         # done. sum is sitting in %eax, the return-value register
```
