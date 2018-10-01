---
title: Extrinsic
---

_Extrinsic_ is a [Substrate](Parity-Substrate) specific generalization which includes _transactions_ (a normal term from the blockchain world) as well as _inherents_ (also Substrate-specific).

In classic blockchains, blocks usually contain pieces of information called transactions that give instructions from "the outside world" (i.e. you and me) to the blockchain logic. For crypto-currency blockchains such instructions are generally confined to variations on the theme of transfering funds from one account to another. Substrate generalises this by removing the assumption that this piece of information be signed, instead merely characterising it on the fact that it "comes from the outside world" and is thus "extrinsic" to the blockchain and its state.

For pieces of information that do nonetheless contain a signature to authenticate whatever data it contains, then we reuse the classic term "transaction". Conversely for those that do not contain a signature, we say they are "inherents", since the chain assumes that by the fact they are finalised means that what they contain is deemed "inherently true". This is reminiscent of the timestamp field in the header of classical chains, which is true simply because enough of the network maintainers (miners or validators) accepted it as being reasonable.

Substrate makes almost no assumptions about the extrinsic format: rather it is up to the [runtime](Runtime.md) to determine whether an extrinsic is valid, and if so what to do about it. The only requirement is that it is prefixed with a 4-byte LE-encoded length of the extrinsic (not including the 4-bytes itself) (making it compatible with `Vec<u8>` in terms of the general Parity Codec). This allows it to be opaquely encoded and decoded and thus be sent around the network and included in transaction pools easily.

Despite Substrate not caring about the extrinsic format at its core, the Substrate Runtime Module Library (SRML) does provide generic implementations of extrinsics which imply their transaction format. Currently two are provided: `UncheckedExtrinsic` and `UncheckedMortalExtrinsic`. The Substrate Node implementation uses the latter. Do note: it's not the only possible option. Other chains, for example a UTXO chain, may define a wildly different format more suitable for their needs.

Extrinsics are expected to be encodable and decodable. See the `Encode` and `Decode` traits.

**Note:** It may seem strange, but currently, Substrate does not define how extrinsics should be encoded. In case of a Polkadot, extrinsics are formed and encoded in the JS part of the code, so you would not find this in the Rust codebase.

### Future-proof

In Polkadot runtime, extrinsics are said to be future-proof. Basically, that means that the actual details of the extrinsic are defined by the runtime, whereas external logic, such as network or storage, treats and handles them as opaque byte vectors.

This allows us to update or extend extrinsic definition by changing the runtime only, without forcing clients to update their software.

### Extrinsic types

Extrinsics fall into two broad categories of which only one is conventional _transactions_. The other is known as _inherents_. The difference between these two is that transactions are signed and gossipped on the network and can be deemed useful per se. This fits the mould of what you would call transactions in Bitcoin or Ethereum.

Inherents, meanwhile, are not passed on the network and are not signed. They represent data which describes the environment but which cannot call upon anything to prove it per se such as a signature. Rather they are assumed to be "true" simply because a sufficiently large number of validators have agreed on them being reasonable.

To give an example, there is the timestamp inherent which sets the current timestamp of the block. This is not a fixed part of Substrate, but does come as part of the Substrate Runtime Module Library to be used as desired. No signature could fundamentally prove that a block were authored at a given time in quite the same way that a signature can "prove" the desire to spend some particular funds. Rather, it is the business of each validator to ensure that they believe the timestamp is set to something reasonable before they agree that the block candidate is valid.

Other examples include the parachain-heads extrinsic in Polkadot and the "note-missed-proposal" extrinsic used in the Substrate Runtime Module Library to determine and punish or deactivate offline validators.

## The Extrinsic format for Node

Substrate Node, as of genesis, defines a specific Extrinsic format, based around the generic `UncheckedMortalExtrinsic` with a standard `Era` format, a Node-specific function format, a 64-bit block number, 64-bit transaction index, a standard `Address` format combining 256-bit `AccountId` with a variable-width 64-bit enumerated `AccountIndex`, and Ed25519 crpypto:

Fields:
- 1-5 bytes: Length of the rest of the extrinsic, as a `Compact` integer in `parity-codec`.
- 1 byte: version information:
  - 7 low bits: version identifier (should be 0b0000001).
  - 1 high bit: signed flag: 1 if this is a transaction (e.g. contains a signature).
- Optional, depending on previous bit: The signature information:
  - 1/3/5/9/33 bytes: The signing account identity, in `Address` format:
    - 0...0xef: 1 byte Account Index, to be interpreted as the value of the byte.
    - 0xfc: 2 byte Account Index, value to follow.
    - 0xfd: 4 byte Account Index, value to follow.
    - 0xfe: 8 byte Account Index, value to follow.
    - 0xff: 32 byte Account ID, value to follow.
  - 64 bytes: The Ed25519 signature of the Signing Payload (detailed below).
  - 8 bytes: The Transaction Index of the signing account (number of signed transactions from the account preceeding this one).
  - 2 bytes: The Transaction Era (detailed below).
- 2+ bytes: The Function Descriptor:
  - 1 byte: The module index.
  - 1 byte: The function index within the module.
  - Variable: Concatenated arguments to the function.

### Address, Account Index and Account ID

An account in Node may be identified in several ways: either directly through its 32-byte public key or `AccountId`; or alternatively through a 64-bit index into an account lookup table stored on-chain known as an `AccountIndex`. If either may be used, then there is an `Address` type which can encode to either of them. This is the type that is used in transactions.

The `AccountId` should be considered the canonical means to identify an account. However, at a difficult-to-compress 32-bytes, it is very heavyweight for a system that has well-under a million accounts. The idea of account indices lets us use a much smaller number to identify accounts provided we are happy that the account will still be in existence when the index comes to be used.

To get maximum efficiency with on-chain storage, `Address`es are encoded as variable length quantities: if it's an index whose value is small enough, the value is encoded as a single byte. If it's an index that fits into 2, 4 or 8 bytes, then it'll be encoded into 3, 5 and 9 bytes respectively. If it's a straight 32-byte identifier, then it'll be encoded into 33 bytes.

The translation from AccountIndex to AccountId, if necessary, is done through a on-chain query provided by the *Balances* module. Account Indexes can be reused if the account becomes dead, implying that any transaction which refers to the account using an Index must be executed in a timely fashion, i.e. while the account is still alive. If the account holder keeps ample funds in it, then it'll be fine, but if they spend it all, then the transaction could become invalid since the Account ID that it is signed with could no longer match the account to which the index later refers.

### Signing Payload

The signing payload is a simple concatenation of four fields, three from the transaction and one derived:

- 8 bytes: The Transaction Index as provided in the transaction itself.
- 2+ bytes: The Function Descriptor as provided in the transaction itself.
- 2 bytes: The Transaction Era as provided in the transaction itself.
- 32 bytes: The hash of the authoring block implied by the Transaction Era and the current block.

### Transaction Era

The Transaction Era is a data structure that, with the help of the Signing Payload, restricts a transaction's validity to a particular period of blocks in a specific blockchain.

Given a Transaction Era instance and a block number from a time recent to when the transaction was authored, it can be used to calculate the *exact* block number at which the transaction was authored. Crucially, if the block number provided is not sufficiently recent (or is before the block that the transaction was authored), then it will provide the wrong answer, which helps make a "out of date" transaction invalid. The level of recent-ness required before the transaction becomes invalid can be configured from 4 blocks through to 65536 blocks, or can be disabled entirely.

It is defined as a 16-bit quantity:

- 4 low bits: The base 2 logarithm minus 1 of the recentness period or zero if disabled.
- 12 high bits: The phase within the period that the transaction was authored.

More formally, given `low4` and `hi12`, we can define `enabled`:

`enabled := low4 > 0`

Assuming `enabled` then we can define:

`period := 2 ** (low4 + 1)`

`phase := hi12 * factor`

Where:

`factor := MAX(1, period / (2 ** 12))`

To determine `phase`, given an authoring block number `current`, then:

`calculate_phase(period, current) := Q(current, factor) % period`

Where:

`Q(n, d) := FLOOR(n / d) * d`

And, given a sufficiently recent block number `recent`, then we can reverse it and get the original authoring block as:

`calculate_current(period, phase, recent) := Q(current - phase, period) + phase`
