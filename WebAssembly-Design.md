---
title: WebAssembly Parity VM Design
---

## Choosing which VM to run.

Parity Ethereum client currently supports 2 VM types - EVM and WebAssembly, with EVM being default. Parity can choose to run WebAssembly VM for the specific contract if and only if BOTH are true:

- WebAssembly is activated on the chain (`wasmActivationTransition` is present in json [chain specification file](Chain-specification.md)).
- The contract's code starts with 4 bytes WASM magic (`00 61 73 6d`, i.e. same as '\0asm').

## Contract ABI

If Parity chooses WebAssembly VM to run the contract, its bytecode should pass ALL of the following checks (each check might represent multiple conditions and links for further reading).

Contract bytecode MUST:

- Be a valid WebAssembly module. WebAssembly validation is a complex process, and is fully covered by [WebAssembly specification](https://webassembly.github.io/spec/core/valid/index.html)
- Contain export `call` resolved as a function with no arguments and no return type.
- NOT contain internal memory, exported or not.
- NOT contain imports of any symbols other than exposed by the [Runtime](WebAssembly-Runtime.md)

If any of those conditions are not met, Parity MUST finish execution of the code with error.

Otherwise, if all conditions are met, Parity instruments the contract code with following modifications:

- [Gas metering injection](WebAssembly-GasMetering.md)
- [Deterministic stack height metering](WebAssembly-StackHeight.md)

Each of the steps above can result in error described in the correspondng links, and Parity MUST stop execution and return error in this case.

Then Parity instantiates the module according to the [WebAssembly specification](https://webassembly.github.io/spec/core/exec/modules.html?highlight=instantiation#exec-instantiation). This instantiation code can also result in a trap, in this case Parity MUST finish execution of the bytecode with error.

If instantiation code produced no trap, Parity executes `call` method according to [WebAssembly Execution specification](https://webassembly.github.io/spec/core/exec/index.html).