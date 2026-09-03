# Integer Arithmetic

Adding two positive numbers can give you a negative result, and `x-y` can disagree with `x-y<0`. These aren't bugs but rather they are artifacts of *finite word size*. Remember: computer "integer" arithmetic is simply modular arithmetic.

## Unsigned Addition

Add column by column, then truncate to $w$ bits. Equivalent to $(x+y) \bmod 2^{w}$. 

```
111
 1001 9
+1100 12
-----
10101 21, needs 5 bits
 0101 5, truncated to 4
```

Overflow occurs if and only if the sum is *smaller than either operand.* 

```c
int uadd_ok(unsigned x, unsigned y) {
   return x + y >= x;
}
```

Overflow subtracts exactly $2^{w}$. 

## Unsigned Negation

$-x$ is whatever gets you back to zero $\bmod 2^{w}$: 
* `x == 0` $\rightarrow$ `0`
* `x > 0` $\rightarrow$ $2^{w}-x$

## Two's-Complement Addition

Identical bit-level procedure to unsigned addition, except for *interpretation* and *overflow*. 

?

## Two's Complement Negation

Complement every bit, and add 1. In C, `-x` and `~x + 1` are the same. 

## Multiplication 

Truncate the full $2w$-bit product to $w$ bits: $(x \cdot y) \bmod 2^{w}$. 

## Multiplication by Constants

## Dividing by Powers of Two
