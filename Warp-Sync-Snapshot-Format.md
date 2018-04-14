---
title: Warp Sync Snapshot Format
---

Warp sync extends previous versions of the protocol with full state snapshots. These snapshots can be used to quickly get a full copy of the state at a given block. Every 5,000 blocks, nodes will take a consensus-critical snapshot of that block's state. Any node can fetch these snapshots over the network, enabling a fast sync. These snapshots have been designed with out-of-order restoration in mind -- it isn't required to get any given chunk before another.

The snapshot format is three-part: block chunks, state chunks, and the manifest.
Every chunk is run through snappy compression, and then hashed. Before compression, chunks are created to be approximately `CHUNK_SIZE` bytes.

`CHUNK_SIZE` = `4MiB`, but this may be subject to change.
All data structures should be assumed to be RLP-encoded unless specified otherwise.

## Manifest
This contains metadata about the snapshot itself, and is used to coordinate snapshots between nodes.
Every snapshot will have a unique manifest, so two identical manifests will refer to the same snapshot.

The manifest is an rlp-encoded list of the following format:
```
[
    version: P, // snapshot format version. Must be set to 2.
    state_hashes: [hash_1: B_32, hash_2: B_32, ...], // a list of all the state chunks in this snapshot
    block_hashes: [hash_1: B_32, hash_2: B_32, ...], // a list of all the block chunks in this snapshot
    state_root: B_32, // the root which the rebuilt state trie should have. used to ensure validity
    block_number: P, // the number of the best block in the snapshot; the one which the state coordinates to.
    block_hash: B_32, // the best block in the snapshot's hash.
]
```

The chunk hashes here are not the hashes of the raw chunk data, but rather of their snappy-compressed data.
This is a canonical identifier for any given chunk.

## Block chunks

*NOTE*: This is the block chunk format for Ethash PoW-based engines. Other consensus engines may have their own block chunk format.

Block chunks contain raw block data: blocks themselves, and their transaction receipts. The blocks are stored in the "abridged block" format (referred to by `AB`), and the the receipts are stored in a list: `[receipt_1: P, receipt_2: P, ...]` (referred to by `RC`).

Each block chunk is an rlp-encoded list of the following format:
```
[
    number: P, // number of the first block in the chunk
    hash: B_32, // hash of the first block in the chunk
    td: B_32, // total difficulty of the first block in the chunk
    [abridged_1: AB, receipts_1: RC], // The abridged RLP for the first block, and its receipts.
    [abridged_2: AB, receipts_2: RC], // The abridged RLP for the second block, and its receipts.
    [abridged_3: AB, receipts_3: RC], // ... and so on.
    ...
]
```

The blocks within must be sequential, so that their abridged RLP can be assembled into entire blocks simply using the metadata at the beginning.

### Abridged block RLP

This differs from the standard block RLP format by omitting some fields which can be reproduced from the metadata at the beginning of the block chunk.

An abridged block takes the following form:
```
[
    // some header fields
    author: B_20,
    state_root: B_32,
    log_bloom: B_256,
    difficulty: B_32,
    gas_limit: B_32,
    gas_used: B_32,
    timestamp: P,
    extra_data: P,

    // uncles and transactions inline
    [tx_1: P, tx_2: P, ...],
    [uncle_1: P, uncle_2: P, ...],

    // Seal fields
    mix_hash: B_32,
    nonce: B_8,
]
```

### Block Chunk Generation

*Definitions:*

Let NUM(X) denote the number of block X.

Let HASH(X) denote the hash of block X.

Let TD(X) denote the total difficulty of block X.

*Algorithm:*

Let B be the most recent block.

Let B<sub>target</sub> = B.

Let B<sub>finish</sub> = `B - 30000 + 1`.

Let S<sub>current</sub> = 0.

Walk backwards from block B until reaching B<sub>finish</sub> .

For each block B<sub>x</sub>:
  - generate the list `[abridged: AB, receipts: RC]` D<sub>x</sub>
  - Note its size: S<sub>x</sub> = SIZE(D<sub>x</sub>).
  - S<sub>current</sub> = S<sub>current</sub> + S<sub>x</sub>.
  - If S<sub>current</sub> > `CHUNK_SIZE`:
    - Let S<sub>current</sub> = S<sub>current</sub> - `CHUNK_SIZE`
    - Build the chunk [ NUM(B<sub>x+1</sub>), HASH(B<sub>x+1</sub>), TD(B<sub>x + 1</sub>), D<sub>x + 1</sub>, D<sub>x + 2</sub>, ..., D<sub>B<sub>target</sub></sub> ]
    - Set B<sub>target</sub> = B<sub>x</sub>.

At the end, if S<sub>current</sub> > 0, write out the remaining chunk from block B<sub>finish</sub> to B<sub>target</sub>:
   [ NUM(B<sub>finish</sub>), HASH(B<sub>finish</sub>), TD(B<sub>finish</sub>), D<sub>finish</sub>, ..., D<sub>B<sub>target</sub></sub> ]

The manifest's `block_chunks` list must contain the chunks' hashes in an ordering such that the hash of any given chunk precedes no hashes of any other chunk which contains blocks with a higher number.

## State Chunks

State chunks store the entire state of a given block. A "rich" account structure is used to save space.
Each state chunk consists of a list of lists, each with two items: an address' `sha3` hash and a rich account structure correlating with it.

We call these lists "account entries".

`[ [hash1: B_32, acc_1: P],  [hash_2: B_32, acc_2: P], ... ]`.

We define the ordering of account to be first lexicographic in the address hash, and if those are equal, then lexicographic in the storage lists of the rich account structure.

### Rich Account

The rich account structure encodes the usual account data such as the nonce, balance, and code, as well as the full storage.

More formally, it is an RLP list in the following format:
```
[
    nonce: B_32,
    balance: B_32,
    code_flag: B_1,
    code: P,
    storage: [[keyhash1: B_32, val1: B_32], [keyhash2: B_32, val2: B_32], ...]
]
```

`code_flag` is a single byte which will determine what the `code` data will be:
  - if `0x00`, the account has no code and `code` is the single byte `0x80`, signifying RLP empty data.
  - if `0x01`, the account has code, and `code` stores an arbitrary-length list of bytes containing the code.
  - if `0x02`, the account has code, and `code` stores a 32-byte big-endian integer which is the hash of the code. The code's hash must be substituted if and only if another account which has a smaller account entry has the same code.

`storage` is a list of the entire account's storage, where the items are RLP lists of length two -- the first item being `sha3(key)`, and the second item being the storage value. This storage list must be sorted in ascending order by key-hash.

If `storage` is large enough that the rich account structure would bring the internal size (see the Validity section) of the chunk to over `CHUNK_SIZE`, only the prefix of `storage` that would keep the internal size of the chunk within `CHUNK_SIZE` will be included. We will call the unincluded remainder `storage'`. A new state chunk will begin with an account entry of the same account, but with storage set to the prefix of `storage'` which will fit in the chunk, and so on.

### Validity

Aside from those given above, there are a couple more requirements for a set of valid state chunks.

We define the internal size S<sub>C</sub> of a chunk C to be the sum of the sizes of the RLP lists contained within.

Any given chunk C has a valid internal size S<sub>C</sub> if and only if S<sub>C</sub> <= `CHUNK_SIZE`.

A set of state chunks S is valid if and only if:

  1. for any two arbitrary selected account entries A<sub>1</sub> and A<sub>2</sub> from any given state chunk S<sub>i</sub> , where A<sub>1</sub> < A<sub>2</sub>, there exists no A<sub>3</sub> from another state chunk S<sub>j</sub> such that A<sub>1</sub> < A<sub>3</sub> < A<sub>2</sub> .
  1. there is no other valid configuration of chunks containing the same data such that for each chunk C<sub>i</sub> , except the one containing the maximum account entry, S<sub>C<sub>i</sub></sub> is a valid internal size. In plainer terms, every chunk except the last must be "maximally packed".
  1. the account entries within any given state chunk must be sorted in ascending order.
  1. if there is only one list A<sub>1</sub> in a state chunk, then there may be no other state chunk containing two account entries A<sub>2</sub> and A<sub>3</sub> such that A</sub>2</sub> < A<sub>1</sub> < A<sub>3</sub>.

The `state_chunks` list in the snapshot manifest must be sorted by the first account entry contained within each one.

## Version history

### Snapshot version 2.
This version introduces the `version` field in the manifest and adds
