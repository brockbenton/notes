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
2. The exponent is offset. ???

## IEEE Representation

## Decoding a Bit Pattern



## Encoding a Decimal Value

## Rounding

Roud-to-even is the default. It only differs from the other modes on *exact ties*; ties go whichever direction makes the last kept bit 0. 

???

## Properties of Operations

*Commutativity always holds.* `a * b` is identical to `b * a`. *Associativity doesn't.* `a + b + c` and `c + b + a` aren't merely reorderings since C evaluates left to right. 

## Casting and Precision Limits
