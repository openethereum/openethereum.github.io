---
title: decl_storage!
---

`decl_storage!` is a macro that defines module specific data entries to be stored on-chain. It is part of the initial set of [runtime](Runtime) module libraries included in this repository, but is not required for [substrate](Parity-Substrate) runtimes and is purely a tool for convenience.

The sole purpose of any blockchain is to provide an interface to store some data in a verifiable and globally irreversible way. When we speak about cryptocurrencies, such storage would typically contain account keys and their actual balances. Generally speaking, you may store arbitrary data, as long as its data type is serializable in substrate terms, i.e. implements `Encode` and `Decode` traits.

Such storage may be altered from runtime when executing an extrinsic, or, in some cases, from the outside.

Internally such storage is represented by some persistent data structure. That means that data is stored in a series of snapshots, each of them may be accessed at a later point in time, but once created snapshots are considered irreversible.

### Persistent storage in a nutshell

For programmers, the easiest way to grasp the idea is to associate it with how Git works. Here, for the sake of simplicity, treat git commit as a block. When working with a Git repo, you have access to the individual commits by their hashes. You may check out particular commit and obtain the snapshot of all files in a repo at that particular point in time. You may also edit some files, add them to the index and commit to the repository. Note, that once pushed upstream, such commit is considered persistent (irreversible). 

In the same manner, you may modify the storage during the block creation phase and then, once the block is sealed, such storage is considered irreversible. You may switch back to any point in block history and get values actual for that block.

So, the most important thing to understand is that in such persistent storage data is never lost. It may be overwritten by new values, but older ones would still remain in the history.

### How to define `decl_storage!` 

The easiest way to understand the syntax of this macro is to look at examples of how it's used in the real code. Below you may find two examples that were taken from the Substrate modules `runtime/timestamp` and `runtime/system` respectively. Instead of trying to grasp the implementation logic (which is irrelevant here), just pay attention to the overall syntax.

So, `timestamp` module defines its storage to contain three types of values, two of which are publicly accessible and the last one is private to the module. They are the timestamp for the current block `Now`, the period between the blocks `BlockPeriod` and the private `DidUpdate` value that's used internally:

```rust
decl_storage! {
        // Here we implementing the storage for the current module
	trait Store for Module<T: Trait>;

	pub Now get(now): b"tim:val" => required T::Moment;

	// The minimum (and advised) period between blocks.
	pub BlockPeriod get(block_period): b"tim:block_period" => required T::Moment;

	// Did the timestamp get updated in this block?
	DidUpdate: b"tim:did" => default bool;
}
```

Storage definition starts from the access specifier (`pub` or empty), then goes the accessor identifiers and, separated by `:`, the namespace and the identifier of the value to be used in the storage. Next goes the type of the value which is separated by `=>`. 

Types may be prepended with the `default` or `required` specifiers, that control the behavior when an attempt is made to read a nonexistent value from the storage: `default` returns the default value associated with a type, `require` panics instead. Otherwise, `Option<T>` is returned where `T` is the actual data type that was specified in a macro.

Storage supports lists and maps as well. The next example shows how to use the map syntax:

```rust
decl_storage! {
	trait Store for Module<T: Trait>;

	pub AccountNonce get(account_nonce): b"sys:non" => default map [ T::AccountId => T::Index ];
	pub BlockHash get(block_hash): b"sys:old" => required map [ T::BlockNumber => T::Hash ];

	pub ExtrinsicIndex get(extrinsic_index): b"sys:xti" => required u32;
	pub ExtrinsicData get(extrinsic_data): b"sys:xtd" => required map [ u32 => Vec<u8> ];
	RandomSeed get(random_seed): b"sys:rnd" => required T::Hash;
	
	// The current block number being processed. Set by `execute_block`.
	Number get(block_number): b"sys:num" => required T::BlockNumber;
	ParentHash get(parent_hash): b"sys:pha" => required T::Hash;
	ExtrinsicsRoot get(extrinsics_root): b"sys:txr" => required T::Hash;
	Digest get(digest): b"sys:dig" => default T::Digest;
}
```

### Accessing values

Accessing values is easy. All you need to do is to use the `StorageValue` type located in the `runtime-support` crate:

```rust
/// A trait for working with macro-generated storage values under the substrate storage API.
pub trait StorageValue<T: Codec> {
	/// The type that get/take return.
	type Query;

	/// Get the storage key.
	fn key() -> &'static [u8];

	/// Does the value (explicitly) exist in storage?
	fn exists() -> bool;

	/// Load the value from the provided storage instance.
	fn get() -> Self::Query;

	/// Store a value under this key into the provded storage instance.
	fn put<Arg: Borrow<T>>(val: Arg);

	/// Clear the storage value.
	fn kill();

	/// Take a value from storage, removing it afterwards.
	fn take() -> Self::Query;
}
```

So, for example, block number from the module above may be accessed like this:
```rust
// Accessing value using generic interface.
// Type would be `Option<T::BlockNumber>`.
let value = <Number<T>>::get();

// Almost the same, but using generated accessor method. 
// Since block number is marked as `required` it would 
// return `T::BlockNumber`, and not the `Option<T::BlockNumber>`.
let value = Self::block_number();
```

The only thing that may cause issues is that type parameter `T`. The thing is that you may access stored data from the module's `impl` block. For example, this is how `set_block_number` is defined in the `system` module:

```rust
impl<T: Trait> Module<T> {
	/// Set the block number to something in particular. Can be used as an alternative to
	/// `initialise` for tests that don't need to bother with the other environment entries.
	#[cfg(any(feature = "std", test))]
	pub fn set_block_number(n: T::BlockNumber) {
		<Number<T>>::put(n);
	}
}
```

For more info on the modules see the `decl_module!` macro.
