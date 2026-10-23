# Chapter 5: Optimizing Program Performance

## What compilers can and can't optimize for you

### Memory references

Suboptimal case (6 memory references): 
```C
void twiddle1(int *xp, int *yp) {
  *xp += *yp;
  *xp += *yp;
}
```

Optimal case (3 memory references):
```C
void twiddle1(int *xp, int *yp) {
  *xp += 2* *yp;
}
```

### The failure in aliasing

*Aliasing* means two pointers point to the same spot in memory. 

```C
x = 1000; y = 3000;
*q = y; /* 3000 */
*p = x; /* 1000 */
t1 = *q; /* 1000 or 3000, depending on wherever q points */
```

Case 1: *p* and *q* point to different places 

```C
/* q --> [3000], p --> [1000] */
/* *p = x doesn't touch q's box so t1 = *q = 3000 */
```

Case 2: *p* and *q* point to the same place (aliased)

```C
/* q --|
/*     |-> [3000] then overwritten --> [1000]
/* p --| 
/* *p = x overwrites the box q also point to so t1 = *q = 1000 */
```

A smart compiler would like to reason: "I just stored y into *q, so *q is 3000. I'll set t1 = 3000 and skip the memory read." That shortcut is only correct in Case 1.

The compiler usually can't prove which case applies. The pointers might come from function arguments, so it can't see what the caller passed in. It therefore has to do the slow, safe thing: actually reload *q from memory after every write through p, just in case (an optimization blocker).

## Cycles per element (CPE)

What *CPE* is: the number of clock cycles a loop spends on each element it processes (each pixel, each array entry). A CPE of 6.5 is superior to a CPE of 10.0. 
* *Overhead* + *CPE * n* 

## First round of code-level fixes

### 0. Turn on compiler optimization

* *Look for:* what optimization flags are being used (`-00`, `-01`, `-02`)
* *Why it matters:* in the book the example's speed was increased by two fold

### 1. Move repeated work out of the loop

* *Look for:* something computed inside a loop, including in the loop condition, that gives the same result every iteration. 
* *Fix:* compute it once before the loop and store it in a local variable.
* *Why the compiler won't:* it can't be sure a function has no side effects, so it leaves calls where they are.

### 2. Reduce procedure calls

* *Look for:* a function called on every iteration just to fetch or check a value, such as getters, accessors, or bounds checks.
* *Fix:* get direct access to the data once, outside the loop, and use it inside. 
* *Why the compiler won't:* same side-effect problem, and it can't inline across boundaries it can't see into.
* *Trade-off:* less modular code.

### 3. Eliminate unneeded memory references

* *Look for:* a loop that reads and writes the same memory location every iteration, such as accumulating into `*ptr`, `array[k]`, or `obj->field`. 
* *Fix:* accumulate in a local variable, which stays in a register, and write it back once after the loop.
* *Why the compiler won't:* aliasing. That pointer might overlap other data the loop reads, so it has to keep going back to memory. 
