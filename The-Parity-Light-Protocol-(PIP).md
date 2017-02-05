# The Parity Light Protocol

This is a proposal/WIP for a robust, efficient, and future-proof light client protocol for ethereum-like chains.
Although inspired by Zsolt Felfoldi's [LES](https://github.com/zsfelfoldi/go-ethereum/wiki/Light-Ethereum-Subprotocol-(LES)), it diverges in several significant ways which will be covered in further sections.

## Motivations

Our goal is to create a protocol which is compatible with varying degrees of "lightness", from clients which store almost nothing to those which store almost everything. The protocol should allow verifiable on-demand data fetching efficiently, with the goal that any given request should require no more than one or two round-trips to the network.

## Previous Work 

LES introduces a few enhancements over the ETH full node protocol towards this end. The first of these is the Canonical Hash Trie (CHT) which maps historical block numbers to their canonical hashes in a merkle trie. This allows us to discard the block headers themselves in favor of a trie root which encompasses the accumulation of their hashes and to fetch proofs that a specific block hash is in fact the one we verified earlier.

Like the LES CHT, a PIP CHT is generated once every 2048 blocks. Unlike the LES CHT, the PIP CHT encompasses just that range of 2048 blocks instead of all blocks from the genesis. So, rather than storing a single 32-byte trie root, we have to store one for every range of 2048 blocks. This makes it a little more difficult to bootstrap a client with a checkpoint, but allows us to actually keep the "CHT-chain" up to date on the light nodes without imposing any additional overhead on full nodes.

LES also introduces a ReqId for coordinating request packets with responses, allowing us to make more than one request of a peer at a time.

## Pruning

We imagine that, given the monotonically growing storage requirements for maintaining a full node, most nodes will prune both historical blocks and block-states in the near future. A heavily-pruned network should not detract from the viability or usability of a light node.

Our proposal is to integrate this with the CHTs, such that full nodes may discard the majority of ranges of blocks, but perhaps maintain the CHT for the ranges which they discard. Full nodes will also be required to maintain specific ranges of blocks and receipts (every nth range of 2048, for example) based on something similar to a Kademlia Node Identifier -- a node's failure to provide blocks it is expected to hold will be considered misbehavior.

Storing ancient block-states is an even larger burden, and it is expected that nodes will store only recent states and prune everything older. Reading ancient state values is a difficult problem, and not a concern of very many use-cases, so we've chosen to ignore it for now.

## Hashes vs. (Number, Index) Tuples

Given a transaction hash, users of the client often expect to be able to immediately access the referenced transaction or its receipt. However, without a storage-heavy mapping from (H256 -> (Block Number, Index)) we have to go to the network to a peer who does hold this mapping, fetch the location information, and then finally the block header and either the body or receipts.

For light clients, this mapping can impose a storage requirement of almost a gigabyte as of block 3,000,000, so maintaining it indefinitely is definitely not an option. Our recommendation is to use (Number, Index) tuples whenever possible to refer to transactions, receipts, and uncles. The choice of (Number, Index) over (Hash, Index) is deliberate: numbers yield information on which peers may hold a given block, while hashes leak no information by design. Furthermore, it's difficult to punish a peer for not providing an answer to a specific transaction or block hash -- you can't distinguish an invalid hash from a misbehaving peer, particularly if the hash refers to a transaction not yet mined or a block which may not have propagated just yet.

## Requests, Batching, and Pipelining

LES allows us to batch requests, but only those of the same type. We propose that in PIP, a single request packet may contain requests of different types, and furthermore that the input of one request in a packet may be the output of a prior request within the same packet. This allows us to minimize the amount of round-trips to the network without reducing the verifiability of our data.

## Request Types and Format

Requests for data are defined in terms of a request ID, various inputs (which are semi-typed), and outputs.
Type definitions:
  - `[T]`: arbitrary-length list of T
  - `U`: unsigned integer
  - `U8`: byte
  - `H256`: 32-byte hash
  - `bool`: byte in {0, 1}
  - `Header`: RLP-encoded block header.
  - `Transaction`: RLP-encoded transaction.
  - `Receipt`: RLP-encoded transaction receipt.

**Request Definitions:**
```
Request::Headers {
    ID: 0
    Inputs:
        0: H256 | U // start
        1: U // skip
        2: U // max
        3: bool // reverse
    Outputs:
        0: [Header] // provided headers
}

Request::HeaderProof {
    ID: 1
    Inputs:
        0: U // block number
    Outputs:
        0: [[U8]] // merkle inclusion proof from CHT
        1: Header // proved header 
        2: H256 // hash
        3: U // td
}

Request::TransactionIndex {
    ID: 2
    Inputs:
        0: H256 // transaction hash
    Outputs:
        0: U // block number
        1: H256 // block hash
        2: U // index
}

Request::BlockReceipts {
    ID: 3
    Inputs:
        0: H256 // block hash
    Outputs:
        0: [Receipt]
}

Request::BlockBody {
    ID: 4
    Inputs:
        0: H256 // block hash
    Outputs:
        0: [Header] // uncles
        1: [Transaction] // txs
}

Request::Account {
    ID: 5
    Inputs:
        0: H256 // block hash
        1: H256 // address hash
        2: U // trie levels to omit
    Outputs:
        0: [[U8]] // merkle inclusion proof from state trie
        1: U // nonce
        2: U // balance
        3: H256 // storage root
        4: H256 // code hash
}

Request::Storage {
    ID: 6
    Inputs:
        0: H256 // block hash
        1: H256 // address hash
        2: H256 // storage key hash
        3: U // trie levels to omit
    Outputs:
        0: [[U8]] // merkle inclusion proof from storage trie
        1: H256 // storage value
}

Request::Code {
    ID: 7
    Inputs:
        0: H256 // code hash
    Outputs:
        0: [U8] // bytecode
}
```

A single request is encoded as an RLP list:
`[ID, [input_0, input_1, ...]]`

The ID is purely for the purposes of discerning each request type.

Where each input is either a single RLP item, or an *output back-reference*, which is a tuple containing two items:
  - the index of a prior request in a packet (the first having index 0)
  - the index of an output of the prior request

This is encoded as an RLP-list containing two items: `[P_IDX, O_IDX]`

Allowing only back-references allows us to never process requests in worse than `O(N)` time, and purely "disjoint" requests (that is, those which don't have dependencies on prior requests) can be processed in parallel.

# PIPv1 Messages:
(Tentative: still need to work in things like flow control, transaction execution, capabilities, metadata, NodeID, pruning responsibilities)

## Handshake
**Status**: 
[`+0x00`, `protocol_version`: `U`, `network_id`: `U`, `genesis_hash`: `H256`, `head_hash`: `H256`, `head_num`: `U`, `head_td`: `U`, `capabilities`: `TODO`]

## Request-response
**Request**:
[`+0x01`, `req_id`: `U`, [`req_1`, `req_2`, ...]]

A unique request identifier followed by a list of requests, in the format described in the requests section.

**Response**:
[`+0x02`, `req_id`: `U`, [`res_1`, `res_2`, ...]]

A response to a set of requests. Each response must be an RLP-encoded list of the correct outputs for its corresponding request. It is permitted to only answer a prefix of the list of requests given, but all responses must be _complete_.