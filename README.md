# Tiny Stack VM

![competition](https://make.fun/badge/tiny-stack-vm-01-run-20261122.svg)
![rank](https://make.fun/badge/tiny-stack-vm-01-run-20261122/robbyensoe/tiny-stack-vm/rank.svg)
![reward](https://make.fun/badge/tiny-stack-vm-01-run-20261122/robbyensoe/tiny-stack-vm/reward.svg)

A complete interpreter for make.fun's tiny stack ISA v1 in one `main.ts`
(Node 22, no dependencies). For each instance under `MAKEFUN_INPUT_DIR`
(default `/input`) it writes the program's exact output bytes to
`stdout.bin` and its exit code to `exit.txt` under `MAKEFUN_OUTPUT_DIR`
(default `/output`).

Semantics follow the kit's reference VM (`vm.mjs`), which ISA.md names as
the rule wherever the two could be read two ways:

- Per-step check order: step limit (trap 10), pc in range (9), opcode
  known (5), immediate fits (9), stack underflow (2), stack overflow (3),
  then the instruction's own traps (4, 6, 7, 8, 11).
- Arithmetic wraps modulo 2^32; `MUL` is `Math.imul`; `-2147483648 / -1`
  is `-2147483648`; any `x % -1` is `0`; remainders take the sign of `a`.
- `DIVU`/`MODU`/`LTU` read operands as unsigned, in the reference's own
  expressions, so results match bit for bit; shift counts are `b & 31`;
  `SHR` is arithmetic, `SHRU` logical.
- `PUSH8` sign-extends; jumps are signed offsets from the next
  instruction; `CALL` takes an unsigned absolute address; `STORE` pops
  address (top) then value; `EXIT` keeps the low byte.
- `PRINT` writes its whole decimal string or none of it at the
  65,536-byte output limit; `IN` yields -1 at and past the input's end;
  falling off the end traps 9, never halts.
