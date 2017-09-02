# General overview

Along with EVM, Parity has an experimental support for Web Assembly as a bytecode for contracts. Some advantages of Web Assembly (WASM for short) are:

- Standard is maintained by huge number of interested parties
- Much less 256-bit word size related overhead
- Much more languages that target WASM (see https://github.com/mbasso/awesome-wasm)
- Advanced optimisation tools for WASM->WASM

# Getting started

To allow your custom chain to take advantage of WASM contracts, just enable them in in your chain spec JSON file under `params` section.

```json
	"params": {
		...
                "wasm": true
	},  
```

[WebAssembly serialization/deserialization in Rust](https://github.com/nikvolf/parity-wasm)