---
title: Deterministic Stack Height
---

## Rationale

Wasm VM in order to execute code uses a stack. Typically the stack contains local variables, call parameters and return addresses. 
This is similar to the stack used in native programs, however, with the exception that in wasm this stack is managed by
the VM and isn't aliasable via linear memory (i.e the stack can't be treated as a byte array and changed arbitrarily). 
Because of this implementation details of the stack aren't observable. Thus it can be implemented in various ways.
Simple implementation of wasm VM could use a heap allocated vector (this is the case for [`wasmi`](https://github.com/paritytech/wasmi)).
More sophisticated implementations (e.g. the ones that use JITs) typically use the native stack.

Native stacks have a few restrictions: they should be continuous and they can't be relocated. This typically means that the whole stack space must be allocated upfront. While this doesn't typically require allocation of physical memory it requires allocation of virtual memory. And this can be a problem for 32-bit machines where address space is scarce.

Imagine an adversarial contract that spent all its gas budget into growing the stack as high as possible. For the execution of this contract, all confirming implementations should provide equal stack size. This might be a problem if a native stack is used: it would require to allocate
enough memory if all the gas is spent to grow the stack.

On the other hand, the size of native stacks used in traditional execution environments does not exceed a few MBs. A typical program uses only a small portion of the native stack. Exceeding the limits of the native stacks is generally due to infinite recursion or similar kind of bugs.

Furthermore, the demand for stack space by the programs doesn't grow these days as opposed to the demand for heap space which is growing constantly.

All these facts make it complicated to do the metering only by gas. The problem might get even worse as gas limit typically grows with the time, thus requiring to allocate more and more virtual memory upfront only serving a purpose for handling degenerative cases.

There are some techniques that might allow to lift restrictions of the native stack such as segmented stacks. But they are generally inferior 
to the straight stack and typically isn't implemented by the production wasm execution environments.

Because of this, our implementation uses deterministic stack limiting instrumentation. This instrumentation counts
how much stack size might use naive wasm VM that puts everything (locals, parameters, stack operands, etc) on the native stack. This limitation provides a way to calculate an upper bound of the native stack usage.

## Instrumentation Pass

This pass introduces a global mutable variable to track stack size,
and instruments all calls with preamble and postamble.

The stack size counter is increased prior the call. Otherwise, the check would
be made after the stack frame is allocated.

The preamble is inserted before the call. It increments
the global stack size variable with statically determined "stack cost"
of the callee. If after the increment the stack size exceeds
the limit then execution traps.
Otherwise, the call is executed.

The postamble is inserted after the call. The purpose of the postamble is to decrease
the stack size counter by the "stack cost" of the callee function.

Note, that we can't instrument all possible ways to return from the function. The simplest
example would be a trap issued by the host function.
That means stack size global won't be equal to zero upon the next execution after such trap.

### Thunks

Because the stack size counter is increased prior the call few problems arise:

- Stack size isn't increased upon entering in the first function, i.e. exported function.
- It is statically unknown what function will be invoked in an indirect call.


The solution for these problems is to generate intermediate functions, called 'thunks', which
will increase the stack size before the call to the original function and decrease it after. 
Then make exported functions and table entries to point to corresponding thunks.

### Stack cost

Stack cost of the function is calculated as the sum of its locals
and the maximal size of the operand stack.

All values are treated equally, as they have the same size.

The rationale behind this is to allow the use of this very naive wasm executor, that is:

- values are implemented by a union, so each value takes a size equal to the size of the largest possible value type this union can hold. (In MVP it is 8 bytes)
- each value from the operand stack is placed on the native stack.
- each local variable and function argument is placed on the native stack.
- arguments pushed by the caller are copied into callee stack rather than shared between the frames.
- upon entry into a function, the entire stack frame is allocated.
