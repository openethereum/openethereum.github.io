---
title: Extrinsic
---

_Extrinsic_ is a [Substrate](Parity-Substrate) specific generalization of the _transaction_ term from the blockchain world.

In classic cryptocurrency blockchains, blocks usually contain transactions to transfer funds from one account to another. But Substrate is way more flexible than that. Basically, Substrate itself neither expects nor defines any specific transaction format. To reflect that, we use the term _extrinsic_ (as opposed to intrinsic) meaning that it's something from the outer world. 

For example, default [runtime](Runtime) for Polkadot expects extrinsics to be of a certain format. For more info, see the [Extrinsic Format](Extrinsic). Still, please keep in mind that it's not the only possible option. Other chains may define a different format more suitable for their needs.

Extrinsics are expected to be decodable. See the `Decode` trait.

**Note:** It may seem strange, but currently, Substrate does not define how extrinsics should be encoded. In case of a Polkadot, extrinsics are formed and encoded in the JS part of the code, so you would not find this in the Rust codebase.

### Future-proof

In Polkadot runtime, extrinsics are said to be future-proof. Basically, that means that the actual details of the extrinsic are defined by the runtime, whereas external logic, such as network or storage, treats and handles them as opaque byte vectors.

This allows us to update or extend extrinsic definition by changing the runtime only, without forcing clients to update their software.

### Extrinsic types

Extrinsics fall into two broad categories of which only one is conventional _transactions_. The other is known as _inherents_. The difference between these two is that transactions are signed and gossipped on the network and can be deemed useful per se. This fits the mould of what you would call transactions in Bitcoin or Ethereum.

Inherents, meanwhile, are not passed on the network and are not signed. They represent data which describes the environment but which cannot call upon anything to prove it per se such as a signature. Rather they are assumed to be "true" simply because a sufficiently large number of validators have agreed on them being reasonable.

To give an example, there is the timestamp inherent which sets the current timestamp of the block. This is not a fixed part of Substrate, but does come as part of the Substrate Runtime Module Library to be used as desired. No signature could fundamentally prove that a block were authored at a given time in quite the same way that a signature can "prove" the desire to spend some particular funds. Rather, it is the business of each validator to ensure that they believe the timestamp is set to something reasonable before they agree that the block candidate is valid.

Other examples include the parachain-heads extrinsic in Polkadot and the "note-missed-proposal" extrinsic used in the Substrate Runtime Module Library to determine and punish or deactivate offline validators.

### Checked vs Unchecked vs Bare

TODO
