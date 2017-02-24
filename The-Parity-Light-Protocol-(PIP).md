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
// Request for block headers.
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

// Request for a header proof.
Request::HeaderProof {
    ID: 1
    Inputs:
        0: U // block number
    Outputs:
        0: [[U8]] // merkle inclusion proof from CHT 
        1: H256 // hash
        2: U // td
}

// Request for transaction index based on hash.
Request::TransactionIndex {
    ID: 2
    Inputs:
        0: H256 // transaction hash
    Outputs:
        0: U // block number
        1: H256 // block hash
        2: U // index
}

// Request for a block's receipts.
Request::BlockReceipts {
    ID: 3
    Inputs:
        0: H256 // block hash
    Outputs:
        0: [Receipt]
}

// Request for a block's transactions
Request::BlockBody {
    ID: 4
    Inputs:
        0: H256 // block hash
    Outputs:
        0: [Header] // uncles
        1: [Transaction] // txs
}

// Request for proof of specific account in the state.
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

// Request for a proof of contract storage.
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

// Request for contract code.
Request::Code {
    ID: 7
    Inputs:
        0: H256 // code hash
    Outputs:
        0: [U8] // bytecode
}

// Request for a specific trie node.
Request::Node {
    ID: 8
    Inputs:
        0: H256 // trie node hash
    Outputs:
        0: [U8]
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

**Inclusion Proofs**:

For state and CHT requests which require merkle inclusion proofs, consider the case where the requested key does not exist in the relevant trie. In these cases, a proof of _non-existence_ may be supplied: the branch of the trie necessary to attempt the lookup for a specific key and prove that the value is not present. This is because an empty response may lead to ambiguity over whether the request simply cannot be served or the peer just refuses to serve it. An "exclusion" proof leaves no doubt in the matter. Furthermore, the other outputs of the request are undefined (that is, they may take any or potentially no values), and requests which (directly or transitively) rely upon should not be responded to.

## Request Credits

For each connection to a remote peer, we maintain an amount of "request credits" which are spent upon each request, a rate of recharge for those credits over time, and a cost table which describes the cost, in credits, of each request. When making a request, it is a violation of the protocol to request more than the current amount of credits allows. Missing responses may lead to a refund the cost of their corresponding request, but are not required to. On response, the server replies with the new amount of credits.

Maintain the following values (given in the handshake, updated via other messages):

**Max Credits**: `U`

The maximum amount of request credits we're allowed to have for this peer.

**Base Cost**: `U`

The base cost (in credits) to be charged for each request packet sent.

**Gas Cost**: `U`

The cost (in credits) for every gas of proved execution.

**Cost Table**: `P`

A table mapping request IDs to costs. An exception is made for the `Headers` request, which may request multiple headers, in which case the cost for the whole request is computed by multiplying `Cost(Headers)` by the maximum number of headers requested.

The cost table is encoded as an RLP list containing lists of length 2:

`[ [id_a; cost_a], [id_b; cost_b], [id_c; cost_c] ]`

Each sub-list contains a request ID and cost in credits, which may not exceed the maximum amount of credits. Sub-lists may be in arbitrary order, but there must be one for every specified request type, with no duplicates.

Define `ID(T)` to be the ID of a request type T.
Define `Cost(ID)` to be the cost for each request type of ID.
The cost calculation function `credit_cost(P)` of a request packet P can be defined as follows (in pseudocode):

```
let mut cost = base_cost;
for request in P {
    match request {
        Request::Headers(inner) => cost += COST(ID(Headers)) * headers_request[2],
        other => cost += COST(ID(request)),
    }
}
```

# PIPv1 Messages:
(Tentative: still need to work in things like flow control, transaction execution, capabilities, metadata, NodeID, pruning responsibilities)

## Handshake
**Status**: 
[`+0x00`, `protocol_version`: `U`, `network_id`: `U`, `genesis_hash`: `H256`, `head_hash`: `H256`, `head_num`: `U`, `head_td`: `U`, `capabilities`: `TODO`]

## Request-response
**Request**:
[`+0x01`, `req_id`: `U`, [`req_1`, `req_2`, ...]]

A unique request identifier followed by a list of requests, in the format described in the requests section.
The base cost of a request packet plus the cumulative cost of all the requests contained therein may not exceed the current number of request credits held by the peer.

**Response**:
[`+0x02`, `req_id`: `U`, `CR`: `U`, [`res_1`, `res_2`, ...]]

A response to a set of requests. The request ID must correspond to the request ID of a corresponding `Request` packet. The `CR` field contains the updated amount of request credits. Each response must be an RLP-encoded list of the correct outputs for its corresponding request. It is permitted to only answer a prefix of the list of requests given, but all responses must be _complete_ (all outputs filled, with the exception of "exclusion proofs" as mentioned above).

**RequestExecutionProof**
[`+0x03`, `req_id`: `U`, `at`: `H256`, `from`: `B_20`, `action`: [`B_20` or []], `gas`: `U`, `gas_price`: `U`, `value`: `U`, `data`: `P`]

Request proof of execution of a transaction from address `from` with action `action`: (encode an address for a call, and an empty RLP list for a create) with the supplied call data and gas amount. `BASE_COST + gas * GAS_COST` may not exceed the amount of request credits held.

`at` is the hash of the block on top of which to execute this transaction.
The correct nonce for `from` within the state of `at` must be used.

**ExecutionProof**
[`+0x04`, `req_id`: `U`, `CR`: `U`, [`item_1`: P, `item_2`: P, ...]]

Returns a fraud proof of execution for the transaction in the corresponding request. `CR` is the amount of request credits remaining. The following item is a list containing, in arbitrary order, all loaded trie nodes and contract code such that the transaction may be re-executed without failure, up to the requested amount of gas.

**UpdateCredits**:
[`+0x05`, `CR`: `U`]

An update to the amount of request credits held by a peer.