---
title: WebAssembly Contract ABI
---

WASM contract is represented by a regular WASM binary and is a subject for WASM validation.

Validation of the contract's code is conducted before it's execution. Code that doesn't pass the validation is rejected and error is returned.

Validation of WASM code is fully covered by [WebAssembly specification](https://webassembly.github.io/spec/core/valid/index.html)). However, OpenEthereum WASM VM imposes additional constraints on the code which are described below.

## Entrypoint

When WASM bytecode is executed, function export with a name `call` will be invoked. This function should have no arguments and return nothing.

Returning control from `call` function will lead to successful contract execution.

## Memory

The only possible way to obtain a linear memory instance for the WASM contract is to import it from the environment. The contract can't declare internal memory. A contract whose declared an internal memory instance will be rejected.

Memory can be imported with a field name `"memory"` from `"env"` module.

Memory import descriptor must always declare maximum number of memory pages, otherwise the contract will be rejected.

The contract can't request more than 16 pages (1 MB) of memory at the moment of writing.

Example of a valid memory import declaration:

```wasm
(module
  (import "env" "memory" (memory 1 1))
  ;; ...
)
```

If the contract doesn't import memory it will not be able to use any instruction that access the memory (such a contract will not pass the validation). Furthermore, any call to a runtime function that accesses a memory will trap.

## Floating-point Operations

All floating-point operations are forbidden at the moment. The contract that contains a floating-point operations will not pass the validation.

## Runtime Functions

The WASM contract can only import functions exposed by the WebAssembly runtime. If WASM contract contains a function import that isn't exposed by the runtime, it will fail to resolve such a function and thus will return the error.

The same also follows for unrecognized imports of global variables and tables.

## Deployment and Constructor

Deployment of a smart-contract is done via a mechanism similar to the EVM's one.

Smart-contract creation transaction should contain the WASM code - deployment script - which will return (See runtime function `ret` in WebAssembly runtime the WASM code for the contract being deployed as a binary blob.

For example:

```wasm
(module
  (import "env" "memory" (memory 1 1))

  ;; ret(data_ptr: i32, data_len: i32)
  ;;
  ;; This function returns binary blob and finishes the 
  ;; execution.
  (import "env" "ret" (func $ret (param i32 i32)))
  
  ;; The minimal possible contract code in WASM.
  ;; By the way, this can also be EVM code if this is something you are into.
  (data (i32.const 4) "\00\61\73\6d\01\00\00\00\01\04\01\60\00\00\03\02\01\00\07\08\01\04\63\61\6c\6c\00\00\0a\04\01\02\00\0b\00\0a\04\6e\61\6d\65\02\03\01\00\00")

  (func (export "call")
    ;; Return the binary blob. In this case this code will be 
    ;; interpreted as a WASM code and will be stored as a code
    ;; for the created account.
    (call $ret
        (i32.const 4) ;; The address of a code of the contract being deployed.
        (i32.const 2048) ;; The length of the code.
    )
  )
)
```
