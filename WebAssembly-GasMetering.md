---
title: Gas metering
---

## Short

- insert external function `gas` from the module `env` in the WebAssembly module being instrumented
- in each function body of instrumented module, add function `gas` invocation at the beginning of each block and prior to every memory grow instruction

## Trivia

Since WebAssembly is a stack machine and does not contain any opcodes that can change "execution pointer" arbitrary (no "calculated jumps"), instrumenting WebAssembly bytecode to transform it to the gas-counting form is trivial and static.

Also, given the available memory for the WebAssembly program is managed with one [grow_memory](https://webassembly.github.io/spec/core/syntax/instructions.html#syntax-instr-memory) opcode, instrumenting memory usage is also trivial and static.

By static we mean that there exist deterministic algorithm G(S, P), where S - schedule, P - oriignal contract program (code) to produce another program P<sup>'</sup> = G(S, P), such as P<sup>'</sup> will put virtual machine in the exact same observable intermediate and final states as P as long as gas limit is not exceeded.

## Schedule

Schedule is a table of execution costs of different WASM opcodes. Since WASM is a very low-level instruction set architecture, cost of most opcodes is 1. Memory costs are described as linear charge per WebAssembly memory page (64kb).

## Details

As discussed, WebAssembly program has statically known points where control flow can hit it during execution (`blocks`). At the beginning of each block, instrumentation sums up all opcodes multiplied the corresponding schedule entry inside the block and inserts invocation of external `gas` function. `Gas` function is inserted in the instrumented code as imported function (from `env` module).

### Example (1)

Imagine the program

```wasm
func $example
  i32.const 5
  i32.neg
end
```

After instrucmentation it becomes

```wasm
import "env" "gas" ($gas)
func $example
  i32.const 3
  call $gas
  i32.const 5
  i32.neg
end
```

Metering of memory, as mentioned, is static as well. Each `grow_memory` opcode is replaced with generated local function that multiplies first operand by the memory page cost and passes it to the external `gas` function.

### Example (2)

Imagine the program

```wasm
func $example
  i32.const 1
  grow_memory
end
```

After instrumentation with schedule where memory cost per page is 4098, it gets transformed into

```wasm
import "env" "gas" ($gas)

func $grow_mem
  $get_local 0
  $get_local 0
  i32.const 4098
  i32.mul
  call $gas
  grow_memory
end

func $example
  i32.const 3
  call $gas
  i32.const 1
  call $grow_mem
end
```