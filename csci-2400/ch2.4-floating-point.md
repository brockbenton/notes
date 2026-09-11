# Floating Point

## Fractional Binary Numbers

Bits left of the *binary point* have weights $2^{i}$; bits right of it have weights $\frac{1}{2}^{i}$. 

* $101.11_{2} = 4 + 0 + 1 + \frac{1}{2} + \frac{1}{4} = \frac{23}{4}$. 
* 2.75 in binary is $10.11$. Slide the point left one spot and it becomes $1.011 \cdot 2^1$. A computer storing $2.75$ as a float is storing two facts: the digits `011` and the exponent `1`. `10.11` $= 2 + 0 + 0.5 + 0.25 = 2.75$. 

## The Three Fields

A float is a fixed number of bits that are chopped into three chunks:
* *Sign.* `0` = positive and `1` = negative.
* *Exponent.* Power of 2 so for 2.75 that's `1`.
* *Fraction.* Digits after the leading 1 so for 2.75 thats `011`.

But a couple of design tricks can make this confusing:
1. *The leading 1 isn't stored.* When a number is normalized, it *always* starts with 1.
2. *The exponent is offset.* $E$ has to be able to go negative, for example: 0.25 is $1.0 \cdot 2^{-2}$. But the exponent field is merely some unsigned bits. Workaround: shift the whole range up before storing. So for $k = 3$ (bias of 3), an exponent of $1$ is stored as $1 + 3 = 4 =$ `100` and an exponent of $-2$ is stored as $-2 + 3 = 1 =$ `001`.

| $k$ | Bias |
| --- | ---- |
| 3   | 3    |
| 4   | 7    |
| 5   | 15   |

So, $\text{Bias} = 2^{k-1} - 1$.

## IEEE Representation

$V = (-1)^{s} \cdot M \cdot 2^{E}$, or in other words, $value = sign \cdot digits \cdot power of two$. 

A distinction: $M$ is `1.011`, but the bits actually stored are `011` so $f = \text{what's physically stored}$ and $M = \text{what the number actually is}$ so $M = 1 + f$. 

|            | Decoding                          | Encoding                          |
| ---------- | --------------------------------- | --------------------------------- |
| Exponent   | $e \rightarrow$ subtract bias $\rightarrow E$ | $E \rightarrow$ add bias $\rightarrow e$ |
| Fraction   | $f \rightarrow$ add 1 $\rightarrow M$ | $M \rightarrow$ drop the 1 $\rightarrow f$ |
| Mode check | Look at the exponent field        | Check $E$ against $1 - \text{Bias}$ |

## Decoding a Bit Pattern

|  | Normalized | Denormalized |
| --- | ---- | ---- |
| $E$ | $e - \text{Bias}$ | $1 - \text{Bias}$ |
| $M$ | $1 + f$ | $f$ |

#### Normalized

*Example.* `1_110_010` formatted with $k = 3$, $n = 3$, so $\text{Bias} = 3$. 

`1_110_010` where `1` $=$ the sign bit, `110` $=$ the exponent field, and `010` $=$ the fraction field. Bias is $2^{k-1} - 1 = 2^{2} - 1 = 3$. 

The process:
1. *Which mode?* Exponent field is `110` (not all zeros, not all ones) so normalized.
2. *Get* $e$. Read the exponent bits as a plain unsigned number. `110` $= 6$. 
3. *Get* $E$. Undo the bias. $E = e - \text{Bias} = 6 - 3 = 3$. 
4. *Get* $2^{E}$. That's the multiplier. $2^{3} = 8$. 
5. *Get* $f$. Read the fraction bits as an unsigned number, then divide by $2^{n}$. `010` $= 2$, and $n = 3$ so divide by $8$. $f = \frac{2}{8} = \frac{1}{4}$. 
6. *Get* $M$. Normalized, so put the hidden 1 back: $M = 1 + f = 1 + \frac{1}{4} = \frac{5}{4}$. 
7. *Multiply.* Sign bit is `1` so $s = -1$. $-1 \cdot 8 \cdot \frac{5}{4} = -10$. 

#### Denormalized

*Example.* `0_0000_101` formatted with $k = 4$, $n = 3$, so $\text{Bias} = 7$. 

The process:
1. *Which mode?* Exponent field is `0000`. 
2. *Get* $e$. Still 0.
3. *Get* $E$. Denormalized locks $E$ at $1 - \text{Bias} = 1 - 7 = -6$. 
4. *Get* $2^{E}$. $2^{-6} = \frac{1}{64}$. 
5. *Get* $f$. `101` $= 5$, over $2^{3} = 8$. $f = \frac{5}{8}$.
6. *Get* $M$. Denormalized has no hidden $1$, so $M = f = \frac{5}{8}$. 
7. *Multiply.* Sign is `0` so positive. $\frac{1}{64} \cdot \frac{5}{8} = \frac{5}{512}$.

## Encoding a Decimal Value

#### Normalized

*Example.* Encode $2.75$. 

The process:
1. *Write it in binary.* $2.75 = 2 + 0.5 + 0.25 \rightarrow$ `10.11`.
2. *Normalize.* `10.11` $\rightarrow$ `1.011 * 2^1`. So $E = 1$.
3. *Normalized or denormalized?* Normalized if $E \geq 1 - \text{Bias}$ so normal rules apply.
4. *Exponent field.* Add the bias back on. $e = E + \text{Bias} = 1 + 3 = 4 \rightarrow$ `100`.
5. *Fraction field.* Take everything after the leading 1, pad right to $n$ bits. `1.011` $\rightarrow$ `011` $\rightarrow$ `0110`.
6. *Sign.* Positive $\rightarrow$ `0`.

Answer: `0_100_0110`.

#### Denormalized

*Example.* Encode $\frac{1}{32} = 0.03125$.

The process:
1. *Write it in binary.* $\frac{1}{32} = 2^{-5} \rightarrow$ `0.00001`.
2. *Normalize.* `0.00001` $\rightarrow$ `1.0 * 2^-5`. So $E = -5$.
3. *Normalized or denormalized?* Denormalized, since $-5 < 1 - \text{Bias} = -2$. Too small to normalize, so $E$ gets locked at $1 - \text{Bias} = -2$.
4. *Exponent field.* All zeros $\rightarrow$ `000`.
5. *Fraction field.* No leading 1 to drop, so $M = f$. Solve $f \times 2^{E} = V$: $f = \frac{1/32}{1/4} = \frac{1}{8}$. Write as $n$ bits — $f \times 2^{n} = \frac{1}{8} \times 16 = 2 \rightarrow$ `0010`.
6. *Sign.* Positive $\rightarrow$ `0`.

Answer: `0_000_0010`.

## Rounding

There's a fixed number of fraction bits, so most numbers don't fit exactly and get nudged to the nearest one that does. Round-to-even is the default. It only differs from the other modes on *exact ties*; ties go whichever direction makes the last kept bit 0. That keeps ties from all drifting the same direction and skewing an average.

Rounding to the nearest half means keeping one bit right of the point. Look at the bits being thrown away:

* Less than halfway (`01`, `001`, ...) $\rightarrow$ round down.
* More than halfway (`11`, `101`, ...) $\rightarrow$ round up.
* Exactly halfway (`10`, `100`, ...) $\rightarrow$ tie, so pick whichever option ends in 0.

| Before     | Decimal | Discarded  | Result    | Decimal |
| ---------- | ------- | ---------- | --------- | ------- |
| $1.001_2$  | 1.125   | `01`, low  | $1.0_2$   | 1.0     |
| $1.011_2$  | 1.375   | `11`, high | $1.1_2$   | 1.5     |
| $11.110_2$ | 3.75    | `10`, tie  | $100.0_2$ | 4.0     |

That last one rounds *up* even though rounding down is equally close, because $11.1_2$ ends in 1 and $100.0_2$ ends in 0.

## Properties of Operations

Every operation returns $\text{Round}(x \odot y)$.

| Property                | Addition | Multiplication     |
| ----------------------- | -------- | ------------------ |
| Commutative             | ✓        | ✓                  |
| Associative             | ✗        | ✗                  |
| Distributive (× over +) | —        | ✗                  |
| Monotonic               | ✓        | ✓ (for $c \geq 0$) |

*Commutativity always holds.* `a * b` is identical to `b * a`. *Associativity doesn't.* `a + b + c` and `c + b + a` aren't merely reorderings since C evaluates left to right, so they group differently and round at different moments:

```
(3.14 + 1e10) - 1e10       ->  0.0     /* 3.14 swallowed, then rounded away */
3.14 + (1e10 - 1e10)       ->  3.14    /* nothing to swallow it */

(1e20 * 1e20) * 1e-20      ->  +inf    /* first product overflows */
1e20 * (1e20 * 1e-20)      ->  1e20

1e20 * (1e20 - 1e20)       ->  0.0     /* doesn't distribute */
1e20 * 1e20 - 1e20 * 1e20  ->  NaN
```

## Casting and Precision Limits

* `int` $\rightarrow$ `float`. Can't overflow, but may round since `float` has a 24-bit significand and `int` has 32 bits.
* `int` or `float` $\rightarrow$ `double`. Always exact. A 53-bit significand holds any 32-bit int with room left over.
* `double` $\rightarrow$ `float`. May overflow to $\pm\infty$, or round.
* `float` or `double` $\rightarrow$ `int`. Rounds toward zero (`1.999` $\rightarrow$ `1`, `-1.999` $\rightarrow$ `-1`), and may overflow.

Whether a `double` expression built from `int`s stays exact is just counting bits against 53:

| Expression from 32-bit ints | Bits needed | Exact? |
| --------------------------- | ----------- | ------ |
| The value itself            | $\leq 32$   | ✓      |
| Sum or difference of two    | $\leq 33$   | ✓      |
| Sum of three                | $\leq 34$   | ✓      |
| Product of two              | $\leq 64$   | ✗      |
| Product of three            | $\leq 96$   | ✗      |

So double addition *is* associative when every operand came from an `int` — nothing ever rounds, so there's no rounding to reorder. Multiplication still isn't, since a 64-bit product already blows past 53 bits.

