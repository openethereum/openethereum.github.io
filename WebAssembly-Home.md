---
title: Web Assembly (WASM)
---
_The development of this feature was supported by the Energy Web Foundation in order to expedite the energy sector revolution towards decentralisation._

## General overview

Along with EVM, Parity has an experimental support for Web Assembly as a bytecode for contracts. Some advantages of Web Assembly (WASM for short) are:

- Standard is maintained by huge number of interested parties
- Much less 256-bit word size related overhead
- Much more languages that target WASM (see [awesome-wasm](https://github.com/mbasso/awesome-wasm)!)
- Advanced optimisation tools for WASM -> WASM

## Getting started

To allow your custom chain to take advantage of WASM contracts, just enable them in in your chain spec JSON file under `params` section by specifying block number starting from which WASM bytecode is allowed to run (`wasmActivationTransition`).

For example, params section might look like this:

```json
	"params": {
		"accountStartNonce": "0x0",
		"maximumExtraDataSize": "0x20",
		"minGasLimit": "0x1388",
		"networkID" : "0x11",
		"wasmActivationTransition": "0x1"
	},
```

Now the engine will be able to distinguish and run WASM byte code if it is used as a contract code. Note that this is the addition to the EVM, so all your EVM contracts on chain are safe and will continue to function as before!

## Building simple contract

Any valid WASM module with the exported `call` function can be used as a contract code. As our codebase is dominated by Rust language, we use experimental Rust tools to compile actual contracts.

Simple contract in Rust language might be as short as this:

```rust
#![no_std]

extern crate pwasm_ethereum as ext;

#[no_mangle]
pub fn call() {
    ext::ret("Hello world");
}
```

Which always return `"Hello world"` string to the caller.

See [Links](WebAssembly-Links.md) examples section for some more sophisticated examples.

## Other languages for contracts

See our [Runtime specification](WebAssembly-Runtime.md). If your languages of choice can be compiled in WebAssembly and can generate bindings for those syscalls, you are good to go.

## Integrating in the existing toolchain

We believe WebAssembly is here to stay, therefore we contribute not only to the Parity to support WASM contract execution, but to WebAssembly ecosystem as a whole.

See our [Links](WebAssembly-Links.md) repositories section for some general-purpose WebAssembly repositories we support.
