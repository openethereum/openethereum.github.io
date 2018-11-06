---
title: Consensus
---

This is the process by which validators agree upon the next valid block to be added to the relay chain.

Once an agreement has been reached, the block is finalised using a forward-verifiable proof. Every block added since the [genesis](Genesis) of the relay chain will have been through this process, thus maintaining consistency.

The consensus itself is reached using a Byzantine fault-tolerant (BFT) algorithm which requires a proportion of the validators to be in agreement before a block is considered valid. Each block in the relay chain can potentially contain details of transactions from many different parachains, so a system-wide, consensus-generating mechanism is required.

In essence, collators located in each parachain collate local transactions into parachain blocks. Each parachain will have a pre-determined group of validators assigned to it and these will form a consensus on the validity of each parachain block. Transactions from valid parachain blocks, along with transactions from other parachains, are referenced in relay chain blocks via a consensus mechanism involving all validators. In this way, the whole [Substrate](Parity-Substrate) ecosystem is covered.

More details can be found [here](https://github.com/w3f/polkadot-spec/blob/master/spec.md#consensus).
