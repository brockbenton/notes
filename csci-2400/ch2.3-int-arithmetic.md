==need to fix ai slop==

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
 
**Identical bit-level procedure to unsigned addition.** Same columns, same carries, same truncation — the machine uses one `add` instruction for both. Only the *interpretation* and the *overflow test* differ.
 
Truncation shifts the true sum by $\pm 2^w$:
 
- true sum $\ge 2^{w-1}$ → **positive overflow**, result is $x + y - 2^w$
- true sum $< -2^{w-1}$ → **negative overflow**, result is $x + y + 2^w$
- otherwise normal
**Overflow test.** Look at the signs, not the carry-out:
 
- both operands **positive**, result **negative** → positive overflow
- both operands **negative**, result **nonnegative** → negative overflow
- mixed signs → **can never overflow**
```c
int tadd_ok(int x, int y) {
    int sum = x + y;
    int neg_over = x < 0 && y < 0 && sum >= 0;
    int pos_over = x >= 0 && y >= 0 && sum < 0;
    return !neg_over && !pos_over;
}
```
 
**Do not test with `sum - x == y`.** Addition and subtraction are both mod $2^w$, so that is true *always* — overflow or not. Useless.
 
4-bit examples:
 
| x | y | true | result | case |
| --- | --- | --- | --- | --- |
| -8 `1000` | -5 `1011` | -13 | 3 `0011` | negative overflow |
| -8 `1000` | 5 `0101` | -3 | -3 `1101` | normal |
| 5 `0101` | 5 `0101` | 10 | -6 `1010` | positive overflow |
 
## Two's-Complement Negation
 
**Procedure: complement every bit, add 1.** In C, `-x` and `~x + 1` are the same thing.
 
```
[0101]  5  →  ~ [1010]  →  +1 [1011]  -5
[1100] -4  →  ~ [0011]  →  +1 [0100]   4
```
 
Shortcut: find the rightmost `1`, leave it and everything right of it alone, flip everything to its left.
 
**The one exception:** $TMin$ is its own negation. `-(-8) == -8` in 4 bits, because `+8` isn't representable. This is the edge case every trick question uses.
 
## Multiplication
 
Truncate the full $2w$-bit product to $w$ bits — $(x \cdot y) \bmod 2^w$.
 
**The truncated bit pattern is identical for signed and unsigned**, so again one instruction serves both. The full products differ; the low $w$ bits don't.
 
| mode | x | y | full | truncated |
| --- | --- | --- | --- | --- |
| unsigned | 5 `101` | 3 `011` | 15 `001111` | 7 `111` |
| two's comp | -3 `101` | 3 `011` | -9 `110111` | -1 `111` |
 
**Overflow test.** Division *does* work here, unlike subtraction for addition:
 
```c
int tmult_ok(int x, int y) {
    int p = x * y;
    return !x || p / x == y;
}
```
 
## Multiplication by Constants
 
Multiply is ~10+ cycles; shift/add/sub are 1. So compilers rewrite it.
 
**Powers of two:** `x * 2^k` → `x << k`. Valid for both signed and unsigned, and stays correct even when it overflows.
 
**Anything else:** write $K$ in binary, find each run of 1s from bit $n$ down to bit $m$, and use either
 
- **Form A:** `(x<<n) + (x<<n-1) + ... + (x<<m)`
- **Form B:** `(x<<n+1) - (x<<m)`
Pick whichever has fewer operations. A run of length 1 or 2 favors A; longer runs favor B.
 
`x * 14`: $14 = 2^3+2^2+2^1$ → `(x<<3)+(x<<2)+(x<<1)`, or $14 = 2^4-2^1$ → `(x<<4)-(x<<1)`. Second one wins.
 
## Dividing by Powers of Two
 
Right shift. **Which shift depends on signedness, and negatives need a fix.**
 
**Unsigned:** `x >> k` logical. Correct, done.
 
**Two's complement:** arithmetic shift rounds *down*; C division rounds *toward zero*. These disagree on negatives. `-9 >> 2` is `-3`, but `-9 / 4` is `-2`.
 
**Fix: add a bias of $2^k - 1$ before shifting, but only when negative.**
 
```c
(x < 0 ? x + (1 << k) - 1 : x) >> k
```
 
Branchless (32-bit), for when conditionals are banned:
 
```c
(x + ((x >> 31) & ((1 << k) - 1))) >> k
```
 
`x >> 31` is all 1s if negative, all 0s if not, so the mask adds the bias or adds nothing.
 
**This only works for powers of two.** There is no shift trick for division by arbitrary constants.
 
## Quick Reference
 
| operation | unsigned | two's complement |
| --- | --- | --- |
| `x + y` | same bits | same bits |
| overflow test | `s < x` | signs agree, result flips |
| `x * y` | same bits | same bits |
| `x * 2^k` | `x << k` | `x << k` |
| `x / 2^k` | `x >> k` (logical) | bias, then `>>` (arithmetic) |
 
