---
title: decl_module!
---

The module declaration. This states the entry points that we handle. The macro takes care of the marshalling of arguments and dispatch.

Anyone can have these functions execute by signing and submitting an extrinsic. Ensure that calls into each of these execute in a time, memory and using storage space proportional to any costs paid for by the caller or otherwise the difficulty of forcing the call to happen.

Generally you'll want to split these into three groups:
- Public calls that are signed by an external account.
- Root calls that are allowed to be made only by the governance system.
- Inherent calls that are allowed to be made only by the block authors and validators.

Information about where this dispatch initiated from is provided as the first argument "origin". As such functions must always look like:

`fn foo(origin, bar: Bar, baz: Baz) -> Result;`

The `Result` is required as part of the syntax (and expands to the conventional dispatch result of `Result<(), &'static str>`).

When you come to `impl` them later in the module, you must specify the full type for `origin`:

`fn foo(origin: T::Origin, bar: Bar, baz: Baz) { ... }`

There are three entries in the `system::Origin` enum that correspond to the above bullets: `::Signed(AccountId)`, `::Root` and `::Inherent`. You should always match against them as the first thing you do in your function. There are three convenience calls in system that do the matching for you and return a convenient result: `ensure_signed`, `ensure_root` and `ensure_inherent`.

```rust
decl_module! {
	// Simple declaration of the `Module` type. Lets the macro know what its working on.
	pub struct Module<T: Trait> for enum Call where origin: T::Origin {
		/// This is your public interface. Be extremely careful.
		/// This is just a simple example of how to interact with the module from the external
		/// world.
		fn accumulate_dummy(origin, increase_by: T::Balance) -> Result;

		/// A privileged call; in this case it resets our dummy value to something new.
		fn set_dummy(new_dummy: T::Balance) -> Result;

		// The signature could also look like: `fn on_finalise()`
		fn on_finalise(_n: T::BlockNumber) {
			// Anything that needs to be done at the end of the block.
			// We just kill our dummy storage item.
			<Dummy<T>>::kill();
		}
	}
}
```
