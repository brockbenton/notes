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

## IEEE Representation

$V = (-1)^{s} \cdot M \cdot 2^{E}$, or in other words, $value = sign \cdot digits \cdot power of two$. 

A distinction: $M$ is `1.011`, but the bits actually stored are `011` so $f = \text{what's physically stored}$ and $M = \text{what the number actually is}$ so $M = 1 + f$. 

## Decoding a Bit Pattern

#### Normalized

*Example.* `1_110_010` formatted with $k = 3$, $n = 3$, so $\text{Bias} = 3$. 

`1_110_010` where `1` $=$ the sign bit, `110` $=$ the exponent field, and `010` $=$ the fraction field. Bias is $2^{k-1} - 1 = 2^{2} - 1 = 3$. 

The walk:
1. *Which mode?* Exponent field is `110` (not all zeros, not all ones) so normalized.
2. *Get $e$.*
3. *Get $E$.*
4. *Get $2^{E}$.*
5. *Get $f$.*
6. *Get $M$.*
7. *Multiply.*

## Encoding a Decimal Value

## Rounding

Roud-to-even is the default. It only differs from the other modes on *exact ties*; ties go whichever direction makes the last kept bit 0. 

???

## Properties of Operations

*Commutativity always holds.* `a * b` is identical to `b * a`. *Associativity doesn't.* `a + b + c` and `c + b + a` aren't merely reorderings since C evaluates left to right. 

## Casting and Precision Limits
