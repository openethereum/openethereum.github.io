---
title: impl_stubs!
---

`impl_stubs!` is a macro that defines entry points to the runtime. It is just one way to define external WASM entry points, which can also be done manually.

Runtime may be seen as an independent library with quite a thin API. Almost all interaction between Substrate and runtime is done by calling one of the functions defined in the `impl_stubs!`.

Typically it is defined as follows:

```rust
pub mod api {
	impl_stubs!(
		version => |()| super::Version::version(),
		authorities => |()| super::Consensus::authorities(),
		initialise_block => |header| super::Executive::initialise_block(&header),
		apply_extrinsic => |extrinsic| super::Executive::apply_extrinsic(extrinsic),
		execute_block => |block| super::Executive::execute_block(block),
		finalise_block => |()| super::Executive::finalise_block(),
		inherent_extrinsics => |(timestamp, heads)| super::inherent_extrinsics(timestamp, heads),
		validator_count => |()| super::Session::validator_count(),
		validators => |()| super::Session::validators()
	);
}
```

Most important functions are:
* `initialise_block` — initialize environment for a new block
* `apply_extrinsic` — apply an extrinsic on top of current state
* `finalize_block` — finalize the state and bake the block

If the block was already created elsewhere it may be imported just by calling the `execute_block` function which is essentially a combination of calls to `initialise_block`, `apply_extrinsic`, and `finalize_block`.
