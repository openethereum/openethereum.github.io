# General overview

Along with EVM, Parity has an experimental support for Web Assembly as a bytecode for contracts. Some advantages of Web Assembly (WASM for short) are:

- Standard is maintained by huge number of interested parties
- Much less 256-bit word size related overhead
- Much more languages that target WASM (see https://github.com/mbasso/awesome-wasm)
- Advanced optimisation tools for WASM->WASM

# Getting started

To allow your custom chain to take advantage of WASM contracts, just enable them in in your chain spec JSON file under `params` section by specifying `wasm: true`.

For example, params section might look like this:

```json
	"params": {
		"accountStartNonce": "0x0",
		"maximumExtraDataSize": "0x20",
		"minGasLimit": "0x1388",
		"networkID" : "0x11",
                "wasm": true
	},  
```

Now the engine will be able to distinguish and run WASM byte code if it is used as a contract code. Note that this is the addition to the EVM, so all your EVM contracts on chain are safe and will continue to function as before! 


# Building a contract

Any valid WASM module with the exported `_call` function can be used as a contract code. As our codebase is dominated by Rust language, we use experimental Rust tools to compile actual contracts. [wasm-tests](https://github.com/paritytech/wasm-tests) repository mighty be a good source of examples. We run actual verification tests against this examples, so you can be sure that those examples are valid piece of functionality.

Simple contract in Rust language might be as short as this:

```rust
#![no_main]
#![no_std]

extern crate wasm_std;

use wasm_std::logger;

#[no_mangle]
pub fn call(_desc: *mut u8) {
    logger::debug("Empty contract");
}
```

Which, once executed, will leave a debugging message `"Empty contract"` in your node console. Please refer to  [wasm-tests README](https://github.com/paritytech/wasm-tests) for prerequisites to setup toolchain which will allow you to build those contracts. 

# Integrating in the existing toolchain

