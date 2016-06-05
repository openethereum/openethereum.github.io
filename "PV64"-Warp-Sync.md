Every N blocks (N is fixed, probably between 1000 and 30000) a new consensus critical snapshot is made.

Snapshot is two-part:

- Part I: All nodes in the state trie. Just the nodes (i.e. not their hashes), concatenated RLP. Also all headers.
- Part II: All uncles, transactions and receipts. Again, concatenated RLP.

Snapshots have three kinds of compressing transformations done:

- For the state trie, hashes are dismissed in favour if indices: All nodes are concatenated in a depth-first-search and their hash added to an array. A map of hash->array_index is then built. The concatenated RLP is then re-traversed, backwards, and all hashes looked up in the mapping and replaced with RLP(reversed_index) (`reversed_index = total_nodes - 1 - index`) before being pushed into a secondary, final, dump. This yields a concatenated RLP dump of nodes where no such node  references an as-yet-unknown node.

- For the headerchain, `parentHash` and `number` may be removed as they're rebuildable on the destination client.

- In both cases, the two basic hashes RLP("") and RLP(0x80) (empty string and empty list resp.) are replaced by a much shorter escape string and the process reversed on decode.

- In both cases, a general-purpose deterministic compression algorithm (e.g. snappy) is used to create the final snapshot.

The snapshot is then split into 16MB chunks and each chunk hashed. A final snapshot-manifest contains all the chunks' hashes and is itself hashed to give the snapshot-hash.

On handshake, the latest snapshot-hash is exchanged along with TD for the snapshot; the manifest is then downloaded by the syncing peer.

The sync strategy is to connect to as many PV64 peers as possible (Parity, initially, but hopefully other clients will implement this, too). That peer should request manifests from other peers to minimise the chance of being duped with dodgy data from a single malicious (and self-harming) node. A headerchain is also requested along with the manifest to help guarantee the state data's validity.

It then proceeds to download the chunks, starting with the first and places each decompressed node into a map of hash->index. This map is used to reinsert the correct hash into later, referencing, nodes.

The downloaded data is checked to make sure (a) the root is as the partial header chain dictates and (b) X random samplings of the headerchain result in good PoW data (X == total blocks / 50) (c) Y random walks result in all hashes being found (Y = 1000?).

The receipts/blocks are downloaded in a second phase and inserted into the database; receipts are checked against the blocks referencing them; blocks are checked against the header chain.