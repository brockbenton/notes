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

### 1. Turn on compiler optimization
