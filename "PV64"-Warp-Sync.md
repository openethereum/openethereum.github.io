Every N blocks (N is fixed, probably between 1000 and 30000) a new consensus critical snapshot is made by all nodes.

Snapshot is two-part:

- Part I: All nodes in the state trie and N headers. For the trie, it's just the nodes (i.e. not their hashes) taken as a concatenated RLP.
- Part II: All blocks and receipts. Again, concatenated RLP.

Snapshots have four kinds of compressing transformations done:

- For the state trie, hashes are dismissed in favour of indices: All nodes are concatenated in a depth-first-search and their hash added to an array; child tries (i.e. contract storage) are output in-place. A map of `hash -> array_index` is then built. The concatenated RLP is then re-traversed, _in reverse_, and all hashes looked up in the mapping and replaced with `RLP(reversed_index)`, where `reversed_index = total_nodes - 1 - index`, before being pushed into a secondary, final, dump. This yields a concatenated RLP dump of nodes where, reading forwards, no node references an as-yet-unknown node. This property is important when decoding.

- For the headerchain, `parentHash` and `number` are replaced by `0x80` (RLP empty string) (since they're rebuildable on the destination client assuming all headers are given in order).

- In all cases, the two basic hashes `SHA3("")` and `SHA3(RLP([]))` (for encoding no contract code, empty storage, empty transaction-trie, empty receipt-trie, no uncles) are replaced by a much shorter escape string (TBD, possibly an RLP dead code, e.g. `0x8100`/`0x8101`) and the process reversed on decode.

The snapshot, as it stands, is then split into 16MB chunks; each chunk is then compressed with a general-purpose deterministic compression algorithm (TBD, perhaps Snappy). A final snapshot-manifest contains all these compressed chunks' hashes and is itself hashed to give the snapshot-hash.

On handshake, the latest snapshot-hash is exchanged along with TD for the snapshot; the manifest is then downloaded by the syncing peer.

The sync strategy is to connect to as many PV64 peers as possible (Parity, initially, but hopefully other clients will implement this, too). That peer should request manifests from other peers to minimise the chance of being duped with dodgy data from a single malicious (and self-harming - you always have to match the bandwidth you trick the victim to lose) node. The header-chain is used to help guarantee the state data's validity.

It then proceeds to download the chunks, starting with the first, and places each decompressed node into a map of hash->index. This map is used to reinsert the correct hash into later, referencing, nodes. This stage can be pipelined substantially.

The downloaded data is checked to make sure (a) the root is as the partial header chain dictates and (b) X random samplings of the headerchain result in good PoW data (X == total blocks / 50) (c) Y random walks result in all hashes being found (Y = 1000?).

Following this, the remaining blocks are downloaded and interpreted as per PV63.

The receipts/blocks are downloaded in a second phase and inserted into the database; receipts are checked against the blocks referencing them; blocks are checked against the header chain.

Parity and Dapps should be mostly usable after the first phase is complete. The second phase should then proceed progressively.

###  Alternative state trie snapshot

Instead of storing trie nodes just save trie values and key hashes. When restoring from the snapshot just re-insert all values by key hash. No additional trie validation is required.