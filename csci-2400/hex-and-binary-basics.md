# Hex and Binary

One hex digit = four bits. So 8 bits is 2 hex digits, 32 bits is 8 hex digits.
The `0x` prefix just marks it as hex.

## Digits

Four bits hold 0–15, and we only have ten numerals, so letters cover 10–15.

| hex | binary | dec | | hex | binary | dec |
|-----|--------|-----|-|-----|--------|-----|
| 0   | 0000   | 0   | | 8   | 1000   | 8   |
| 1   | 0001   | 1   | | 9   | 1001   | 9   |
| 2   | 0010   | 2   | | a   | 1010   | 10  |
| 3   | 0011   | 3   | | b   | 1011   | 11  |
| 4   | 0100   | 4   | | c   | 1100   | 12  |
| 5   | 0101   | 5   | | d   | 1101   | 13  |
| 6   | 0110   | 6   | | e   | 1110   | 14  |
| 7   | 0111   | 7   | | f   | 1111   | 15  |

`f` is 15, not 16. 16 needs two digits: `0x10`.

## Two digits

Place value, but each place is 16× the one right of it.
`0x47` = 4×16 + 7 = 71.

Or just expand each digit to 4 bits and concatenate:

```
0x3a  →  0011 1010  = 58
0x1f  →  0001 1111  = 31
```

| hex    | dec |
|--------|-----|
| `0x1f` | 31  |
| `0x3a` | 58  |
| `0xaa` | 170 |
| `0xff` | 255 |

## Landmarks

| hex    | dec | |
|--------|-----|-|
| `0xf`  | 15  | nibble of 1s |
| `0x10` | 16  | |
| `0x1f` | 31  | low 5 bits |
| `0x7f` | 127 | low 7 bits |
| `0xff` | 255 | byte of 1s |

A run of n 1s = 2ⁿ − 1.

## Width changes meaning

`10000000` is:

- −128 as 8-bit signed (leading bit is the sign)
- 128 as 8-bit unsigned
- 128 as the low byte of a 32-bit int (the sign bit is bit 31, which is 0)
