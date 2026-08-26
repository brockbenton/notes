# Bit-Level Operations in C

* &, |, $\neg$

View arguments as bit vectors. Each argument applied bit-wise.

* $\neg 0x41 \rightarrow \neg (0100$ $0001_{2}) = 1011$ $1110_{2} \rightarrow 0xBE$
* $\neg 0x00 \rightarrow \neg (0000$ $0000_{2}) = 1111$ $1111_{2} \rightarrow 0xff$ 
* $0x69$ & $0x55 \rightarrow $
* $0x69 | 0x55$

# Logic Operations in C

* &&, ||, !
  * View 0 as "false" and everything nonzero as "true"
  * Always return 0 or 1
 
Examples,

* ! $0x41 \rightarrow !$ True $=$ False
* ! $0x00 \rightarrow !$ False $=$ True
* !! $0x41 \rightarrow !!$ True $\rightarrow !$ False $=$ True
* $0x69$ && $0x55 \rightarrow$ True
* $0x69 || 0x55 \rightarrow$ True
