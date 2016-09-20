Warp sync extends previous versions of the protocol with full state snapshots. These snapshots can be used to quickly get a full copy of the state at a given block. Every 30,000 blocks, nodes will take a consensus-critical snapshot of that block's state. Any node can fetch these snapshots over the network, enabling a fast sync.

The snapshot format is three-part: block chunks, state chunks, and the manifest.
Every chunk is run through snappy compression, and then hashed. Before compression, chunks are created to be approximately `CHUNK_SIZE` bytes. 

`CHUNK_SIZE` = `4MiB`, but this may be subject to change.

## Manifest
This contains metadata about the snapshot itself, and is used to coordinate snapshots between nodes.
Every snapshot will have a unique manifest, so two identical manifests will refer to the same snapshot.

The manifest is an rlp-encoded list of the following format:
```
[
    state_hashes: [hash_1: B_32, hash_2: B_32, ...], // a list of all the state chunks in this snapshot
    block_hashes: [hash_1: B_32, hash_2: B_32, ...], // a list of all the block chunks in this snapshot
    state_root: B_32, // the root which the rebuilt state trie should have. used to ensure validity
    block_number: P, // the number of the best block in the snapshot; the one which the state coordinates to.
    block_hash: B_32, // the best block in the snapshot's hash.
]
```

## Block chunks
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

The blocks within are assumed to be sequential, so that their abridged RLP can be assembled into entire blocks simply using the metadata at the beginning.

## Abridged block RLP

This differs from the standard block RLP format by omitting some fields which can be reproduced from the metadata at the beginning of the block chunk.

An abridged block takes the following form:
```
[
    // some header fields
    author: B_20,
    state_root: B_32,
    transactions_root: B_32,
    receipts_root: B_32,
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

## Block Chunk Generation
Let B be the most recent block.
Let B<sub>target</sub> = B. 
Let S<sub>current</sub> = 0. 

Walk backwards from block `B` until reaching `B - 30000`.

For each block B<sub>x</sub>, generate the list
`[abridged: AB, receipts: RC]` D<sub>x</sub>, note its size, S<sub>x</sub>, and set S<sub>current</sub> to S<sub>x</sub>.
If S<sub>current</sub> > `CHUNK_SIZE`, let S<sub>current</sub> = S<sub>current</sub> - `CHUNK_SIZE` and then
  build the chunk [ NUM(B<sub>x+1</sub>), HASH(B<sub>x+1</sub>), TD(B<sub>x + 1</sub>), D<sub>x + 1</sub>, D<sub>x + 2</sub>, ..., D<sub>B<sub>target</sub></sub> ]. Set B<sub>target</sub> = B<sub>x</sub>.

At the end, if S<sub>current</sub> > 0, write out the remaining chunk from block `B - 30000` to B<sub>target</sub>.
