# Shifting Bit Vectors

Left shift fills with 0s.
* $1011$ $1110_2$ << $3$ becomes $1111$ $0000_2$

Right shift depends on the operand's type.
* Logical (unsigned): $1011$ $1110_2$ >> $3$ becomes $0001$ $0111_2$
* Arithmetic (signed): $1011$ $1110_2$ >> $3$ becomes $1111$ $0111_2$
