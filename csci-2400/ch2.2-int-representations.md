# Integer Representations

A bit pattern like `1011` has no inherent numeric value. *Encoding* is the rule that the user and computer agree on for turning that pattern into a number. Two rules that we will discuss, include: *unsigned encoding* and *two's-complement encoding*.

## Integral Data Types

An *integral* data type is one that represents a finite range of integers. For example, `char, short, long,` and `long long` are all integral data types. Each integral data type can be expressed as non-negative (`unsigned`) or possibly negative (`signed`) which is the default. 

<img width="494" height="250" alt="image" src="https://github.com/user-attachments/assets/ecc073d5-b787-4c00-b3fc-fb8f1c5ee612" />

<img width="494" height="250" alt="image" src="https://github.com/user-attachments/assets/bbba4e73-7b09-4f5d-88ca-730805fa9a8a" />

## Unsigned Encodings

This is regular binary place-value counting. Similar to decimal, where each position is worth 10x the one to its right (1s, 10s, 100s...), binary positions are worth 2x the one to its right (1s, 2s, 4s, 8s...). Since every position is positive, you can never get a negative number - hence "unsigned."

$B2U_{w}(x) = \sum_{i=0}^{w-1} x_{i}2^{i}$ with range: $[0, 2^{w-1}]$

So for bits = `1011`, unsigned = `8 + 0 + 2 + 1 = 11`. 

## Two's-Complement Encodings

Exact same rule as unsigned encoding, except the leftmost bit's weight gets flipped negative. For example, `(8, 4, 2, 1)` for 4 bits is `(-8, 4, 2, 1)`. 

$B2T_{w}(x) = -x_{w-1}2^{w-1} + \sum_{i=0}^{w-1} x_{i}2^{i}$ with range: $[-2^{w-1}, 2^{w-1} -1]$

So for bits = `1011`, two's-complement = `-8 + 0 + 2 + 1 = -5`. 

An 8 bit example for -35 looks very similar,
| -128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|---|---|---|---|---|---|---|---|
| 1 (-128) | 1 (64) | 0 | 1 (16) | 1 (8) | 1 (4) | 0 | 1 (1) |
 
$-128 + 64 + 16 + 8 + 4 + 1 = -35$

#### Encoding a Negative Decimal as Two's-Complement

$B2T$ goes bits $\rightarrow$ decimal. To go the opposite direction, use the following algorithm:
1. Write the magnitude (the positive version of the number) in ordinary binary.
2. Invert every bit.
3. Add 1.

For example, encoding -35 as an 8-bit two's complement number:
1. 35 in binary: `00100011`
2. Invert every bit: `11011100`
3. Add 1: `110111101`

## Conversions Between Signed and Unsigned

C allows for casting between `unsigned` and `signed`: `(unsigned) x` and `(int) u`. Doing this, though, might yield unexpected results. For example, consider the following code:

```c
short int v = -12345;
unsigned short uv = (unsigned short) v;
printf("v = %d, uv = %u\n", v, uv);
```

When run on a two's-complement machine, the following output is generated:

`v = -12345, uv = 53191`

While the numeric value changed, the bit values are identical. 

## Signed vs. Unsigned in C

Most numbers in C are signed by default. When declaring a constant such as `12345` or `0x1A2B,` the value is considered signed. Appending the character `U` or `u` creates an unsigned constant, e.g., `12345U` or `0x1A2Bu`. 

Conversions can happen explicitly (as described in **Conversions Between Signed and Unsigned**), or implicitly, like the following, 
```c
int tx, ty;
unsigned ux, uy;

tx = ux; /* Cast to signed */
uy = ty; /* Cast to unsigned */
```

Printing numeric values can be done via format specifiers (`%d` for signed and `%u` for unsigned). 

## Expanding the Bit Representation of a Number

Widening (e.g. `short` $\rightarrow$ `int`): 
* *Unsigned.* zero-extend, pad new high bits with `0`.
* *Two's-complement.* sign-extend, pad new high bits with copies of the original sign bit.

?

## Truncating Numbers

Shrinking (e.g. `int` $\rightarrow$ `short`): drop high-order bits.
* *Unsigned.* equivalent to `x mod 2^{k}`.
* *Two's-complement.* also `x mod 2^{k}`, but then reinterpreted via `U2T`. 

## Advice on Signed vs. Unsigned

Since the conversion of signed to unsigned causes unintuitive behavior, it is best to not use unsigned numbers. In fact, C is one of the only languages that support unsigned integers. 
