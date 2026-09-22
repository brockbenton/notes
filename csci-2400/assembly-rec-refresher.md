# Assembly: Recitation Refresher

### Opcode

*opcode.* Instruction for the computer: e.g., `push`, `sub`, `mov`, `callq`, `cmpl`, `jns`.

Some example `mov` instructions might include,
* `mov rdi, 8` which means "move into the register rdi, the constant 8"
* `mov rdi, rsi`
* `mov rdi, qwordptr [rsi]` which means "move into rdi, the quad-word pointer of rsi (value inside rsi)"

### Registers 

*registers.* Start with "`%`": e.g., `%rbp`, `%rbx`, `%rsp`, `%rsi`.

These sit within the CPU and are very (!) quick memory. If we are coding in 64-bit assembly, then each register is 64-bits wide.

Some important registers include,
  * *Return Value.* `%rax`
  * *Stack Pointer.* `%rsp`
  * *6 Argument Registers.*
  * *Caller-saved.*
  * *Callee-saved.*

### Constants

*constant.* Start with `$`: e.g. `$0x28` (numeric constant in hexadecimal).

### Memory Access

*memory access.* Location wrapped with parentheses: e.g. `(%rsp)`.
