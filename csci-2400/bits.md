# Everything is Bits

* Each bit is 0 or 1
* Through encoding + interpretation, sets of bits can represent and manipulate data.

## Encoding Byte Values

* 8 bits = 1 byte.
* How many combinations can we generate? $2^8 = 256$.

We can represent integer values,
*   $0000$ $0000_2 = 0_10$
*   $0000$ $0001_2 = 1_10$
*   $0000$ $0010_2 = 2_10$
*   ...
*   $0000$ $1001_2 = 9_10$
*   $0000$ $1010_2 = 10_10$
  
What about converting from binary to decimal?
*   $0110$ $0111$
*   $\sum_{w-1}^{i=0} x_{i} \cdot Z^{i}$ where $w$ is the width
*   $= x_0 2^0 + x_1 2^1 + \dots + x_7 2^7$
*   $= 1 \cdot 2^0 + 1 \cdot 2^1 + \cdots + 0 \cdot 2^7$

## Hex number base

* Another representation of binary
* Base 16 (0 to 9, A to F)

<img width="860" height="330" alt="image" src="https://github.com/user-attachments/assets/38045eba-594f-4a9f-95cb-d7e0ebb90d75" />

