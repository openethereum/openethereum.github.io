---
title: WebAssembly OpenEthereum VM Design
---

## Choosing which VM to run.

OpenEthereum client currently supports 2 VM types - EVM and WebAssembly, with EVM being default. OpenEthereum can choose to run WebAssembly VM for the specific contract if and only if BOTH are true:

- WebAssembly is activated on the chain (`wasmActivationTransition` is present in json [chain specification file](Chain-specification)).
- The contract's code starts with 4 bytes Wasm magic (`00 61 73 6d`, i.e. same as '\0asm').

## Running the code

If OpenEthereum chooses WebAssembly VM to run the contract, it's bytecode should satisfy the constraints of [contract's ABI](WebAssembly-ABI). If it doesn't satisfy all constraints such a contract will be rejected and the error returned.

Before the execution of the Wasm contract it's code will be instrumented with the following modifications:
- [Gas metering injection](WebAssembly-GasMetering)
- [Deterministic stack height metering](WebAssembly-StackHeight)

Each of the steps above can result in error described in the correspondng links, and OpenEthereum MUST stop execution and return the error in this case.

Then OpenEthereum instantiates the module according to the [WebAssembly specification](https://webassembly.github.io/spec/core/exec/modules.html?highlight=instantiation#exec-instantiation). This instantiation code can also result in a trap, in this case OpenEthereum MUST finish execution of the bytecode with the error.

If the instantiation code produced no trap, OpenEthereum executes exported function with a name `call` according to [WebAssembly Execution specification](https://webassembly.github.io/spec/core/exec/index.html).
