# Integer Representations

#### Integral Data Types

An *integral* data type is one that represents a finite range of integers. For example, `char, short, long,` and `long long` are all integral data types. Each integral data type can be expressed as non-negative (`unsigned`) or possibly negative (`signed`) which is the default. 

<img width="494" height="250" alt="image" src="https://github.com/user-attachments/assets/ecc073d5-b787-4c00-b3fc-fb8f1c5ee612" />

<img width="494" height="250" alt="image" src="https://github.com/user-attachments/assets/bbba4e73-7b09-4f5d-88ca-730805fa9a8a" />

#### Unsigned Encodings



#### Two's-Complement Encodings



#### Conversions Between Signed and Unsigned

C allows for casting between `unsigned` and `signed`: `(unsigned) x` and `(int) u`. Doing this, though, might yield unexpected results. For example, consider the following code:

```c
short int v = -12345;
unsigned short uv = (unsigned short) v;
printf("v = %d, uv = %u\n", v, uv);
```

When run on a two's-complement machine, the following output is generated:

`v = -12345, uv = 53191`

While the numeric value changed, the bit values are identical. 

#### Signed vs. Unsigned in C

#### Expanding the Bit Representation of a Number

#### Truncating Numbers

#### Advice on Signed vs. Unsigned

Since the conversion of signed to unsigned causes unintuitive behavior, it is best to not use unsigned numbers. In fact, C is one of the only languages that support unsigned integers. 
