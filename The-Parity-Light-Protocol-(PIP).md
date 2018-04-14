---
title: The Parity Light Protocol
---

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

An alternative is to delegate responsibilities of serving blocks based on a prefix of the block's hash as opposed to the block's number. While this will definitely lead to a well-partitioned mesh, it makes synchronization much less efficient as it becomes impossible to reliably request ranges of ancient blocks from any given peer due to the fact that the longer a list of hashes of sequential blocks gets, the more unlikely it is for all hashes to share a common prefix.

Storing ancient block-states is an even larger burden, and it is expected that nodes will store only recent states and prune everything older. Reading ancient state values is a difficult problem, and not a concern of very many use-cases, so we've chosen to ignore it for now.

## Hashes vs. (Number, Index) Tuples

Given a transaction hash, users of the client often expect to be able to immediately access the referenced transaction or its receipt. However, without a storage-heavy mapping from (H256 -> (Block Number, Index)) we have to go to the network to a peer who does hold this mapping, fetch the location information, and then finally the block header and either the body or receipts.

This mapping can impose a storage requirement of almost a gigabyte as of block 3,000,000, so maintaining it indefinitely for light clients is definitely not an option. Our recommendation is to use (Number, Index) tuples whenever possible to refer to transactions, receipts, and uncles. The choice of (Number, Index) over (Hash, Index) is deliberate: numbers yield information on which peers may hold a given block, while hashes leak no information by design. Furthermore, it's difficult to punish a peer for not providing an answer to a specific transaction or block hash -- you can't distinguish an invalid hash from a misbehaving peer, particularly if the hash refers to a transaction not yet mined or a block which may not have propagated just yet.

In chains where [EIP86](https://github.com/ethereum/EIPs/pull/208) has been implemented, the same transaction can be included in the chain multiple times, making transaction hash an even less convenient identifier for a transaction.

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
        Loose(H256 | U) // start 
        U // skip
        U // max
        bool // reverse
    Outputs:
        [Header] // provided headers
}

// Request for a header proof.
Request::HeaderProof {
    ID: 1
    Inputs:
        Loose(U) // block number
    Outputs:
        [U8](U8) // merkle inclusion proof from CHT 
        H256 reusable_as(0) // hash 
        U // td
}

// Request for transaction index based on hash.
Request::TransactionIndex {
    ID: 2
    Inputs:
        Loose(H256) // transaction hash
    Outputs:
        U reusable_as(0) // block number
        H256 reusable_as(1) // block hash
        U // index in the block.
}

// Request for a block's receipts.
Request::BlockReceipts {
    ID: 3
    Inputs:
        Loose(H256) // block hash
    Outputs:
        [Receipt] // block receipts
}

// Request for a block's transactions
Request::BlockBody {
    ID: 4
    Inputs:
        Loose(H256) // block hash
    Outputs:
        [Header] // uncles
        [Transaction] // txs
}

// Request for proof of specific account in the state.
Request::Account {
    ID: 5
    Inputs:
        Loose(H256) // block hash
        Loose(H256) // address hash
    Outputs:
        [U8](U8) // merkle inclusion proof from state trie
        U // nonce
        U // balance
        H256 reusable_as(0) // code hash
        H256 reusable_as(1) // storage root
}

// Request for a proof of contract storage.
Request::Storage {
    ID: 6
    Inputs:
        Loose(H256) // block hash
        Loose(H256) // address hash
        Loose(H256) // storage key hash
    Outputs:
        [U8](U8) // merkle inclusion proof from storage trie
        H256 reusable_as(0) // storage value
}

// Request for contract code.
Request::Code {
    ID: 7
    Inputs:
        Loose(H256) // block hash
        Loose(H256) // code hash
    Outputs:
        [U8] // bytecode
}

// Request for proof of execution
Request::Execution {
    ID: 8
    Inputs:
        Loose(H256) // block hash
        Address // address from
        Address | () // action: call if address, create if empty.
        U256 // gas to prove
        U256 // gas price
        U256 // value to transfer
        [U8] // call data
    Outputs:
        [U8](U8) // state items necessary to prove execution
}
```

A single request is encoded as an RLP list:
`[ID, [input_0, input_1, ...]]`

A response is encoded similarly:
`[ID, [output_0, output_1, ...]]`

The ID is purely for the purposes of discerning each request and response type.

Inputs are either "fixed" or "loose". Loose inputs may be an *output back-reference*, which is a tuple containing
two items:
  - the index of a prior request in a packet (the first having index 0)
  - n, where some output of that request type is marked as `reusable_as(n)`.

Back-references are encoded as an RLP-list containing two items: `[P_IDX, O_IDX]`.

Allowing only back-references allows us to never process requests in worse than `O(N)` time, and purely "disjoint" requests (that is, those which don't have dependencies on prior requests) can be processed in parallel.

All loose inputs are marked as `Loose(T)` in the request definition. Any other input is fixed.
Fixed inputs are just the RLP-encoded value. Loose inputs are a RLP-encoded list of two elements:
[`discriminant`: `U8`, `data`].

If the `discriminant` is 0, `data` is an RLP-encoded value of the expected type.
If the `discriminant` is 1, `data` is an output back-reference, as described above.
All other discriminant values are undefined and are not valid under PIP.

**Inclusion and Exclusion Proofs**:

For state and CHT requests which require merkle inclusion proofs, consider the case where the requested key does not exist in the relevant trie. In these cases, a proof of _non-existence_ may be supplied: the branch of the trie necessary to attempt the lookup for a specific key and prove that the value is not present. This is because an empty response may lead to ambiguity over whether the request simply cannot be served or the peer just refuses to serve it. An "exclusion" proof leaves no doubt in the matter. Furthermore, the other outputs of the request should take the default values under the chain's consensus rules at that block: for an account, this might be an empty balance, storage trie root, and nonce. For storage, this might be the default zeroed value.

**Response Completeness**:
A response is considered _complete_ if all outputs are filled. A headers request has the additional constraint of being complete if and only if the list of returned headers is non-empty.

## Request Credits

For each connection to a remote peer, we maintain an amount of "request credits" which are spent upon each request, a rate of recharge for those credits over time, and a cost table which describes the cost, in credits, of each request. When making a request, it is a violation of the protocol to request more than the current amount of credits allows. Missing responses may lead to a refund the cost of their corresponding request, but are not required to. On response, the server replies with the new amount of credits.

Maintain the following values (given in the handshake, updated via other messages):

**Max Credits**: `U`

The maximum amount of request credits we're allowed to have for this peer.

**Base Cost**: `U`

The base cost (in credits) to be charged for each request packet sent.

**Cost Table**: `P`

A table mapping request IDs to costs. Exceptions are made for the `Headers` and `Execution` requests, which may request multiple headers or a certain amount of gas to prove, respectively. In these cases the cost for the whole request is computed by multiplying `Cost(ID)` by the maximum number of headers or gas requested, respectively.

The cost table is encoded as an RLP list containing the base cost followed by lists of length 2:

`[ base_cost, [id_a; cost_a], [id_b; cost_b], [id_c; cost_c] ]`

Each sub-list contains a request ID and cost in credits, which may not exceed the maximum amount of credits. Sub-lists may be in arbitrary order, but there must be one for every specified request type, with no duplicates.

Define `ID(T)` to be the ID of a request type T.
Define `Cost(ID)` to be the cost for each request type of ID.
The cost calculation function `credit_cost(P)` of a request packet P can be defined as follows (in pseudocode):

```
let mut cost = base_cost;
for request in P {
    match request {
        Request::Headers(inner) => cost += COST(ID(Headers)) * inner.max,
        Request::Execution(inner) => cost += COST(ID(Execution)) * inner.gas,
        other => cost += COST(ID(request)),
    }
}
```

**Rate of Recharge**: `U`
The rate of recharge (in credits) of our local credits per second.

## PIPv1 Messages:

### Handshake
**Status**: 
[`+0x00`, [`key_0`, `value_0`], [`key_1`, `value_1`], ...] Inform a peer of the sender's current state. This message should be sent _after_ the initial handshake and _prior_ to any PIP related messages. The following keys should be present (except the optional ones) in order to be accepted by a PIP/1 node: (value types are noted after the key string)

* "protocolVersion" `P`: is 1 for the PIPV1 protocol version.
* "networkId" `P`: should be 0 for testnet, 1 for mainnet.
* "headTd" `P`: Total Difficulty of the best chain. Integer, as found in block header.
* "headHash" `B_32`: the hash of the best (i.e. highest TD) known block.
* "headNum" `P`: the number of the best (i.e. highest TD) known block.
* "genesisHash" `B_32`: the hash of the Genesis block.
* "serveHeaders" (optional, no value): present if the peer can serve header chain downloads.
* "serveChainSince" `P` (optional): present if the peer can serve Body/Receipts ODR requests starting from the given block number.
* "serveStateSince" `P` (optional): present if the peer can serve Proof/Code ODR requests starting from the given block number.
* "txRelay" (optional, no value): present if the peer can relay transactions to the network.
* "flowControl/BL" `P` (optional): Max credits,
* "flowControl/MRC" (optional): Cost table,
* "flowControl/MRR" (optional): Rate of recharge,

If any of the flow control keys are missing, this peer is not a server and cannot be requested from.

**Announcement**
[`+0x01`, `headHash`: `B_32`, `headNum`: `U`, `headTd`: `U`, `reorgDepth`: `U`, [`key_1`: `val_1`], [`key_2: `val_2`], ...]

Announce a new chain head, along with a reorganization depth to the common ancestor of the new head and the last announced head. Also update any of the key-value pairs given in the handshake. 

### Request-response
**Request**:
[`+0x02`, `req_id`: `U`, [`req_1`, `req_2`, ...]]

A unique request identifier followed by a list of requests, in the format described in the requests section.
The base cost of a request packet plus the cumulative cost of all the requests contained therein may not exceed the current number of request credits held by the peer.

**Response**:
[`+0x03`, `req_id`: `U`, `CR`: `U`, [`res_1`, `res_2`, ...]]

A response to a set of requests. The request ID must correspond to the request ID of a corresponding `Request` packet. The `CR` field contains the updated amount of request credits. Each response must be an RLP-encoded list of the correct outputs for its corresponding request. It is permitted to only answer a prefix of the list of requests given, but all responses must be _complete_.

### Modifying request credits parameters mid-connection.

**UpdateCreditParameters**:
[`+0x04`, `max`: `U`, `recharge`: `U`, `cost_table`: `CT`]

Send a remote peer new request credits parameters: a new maximum, rate of recharge per second, and cost table.

A peer receiving this packet should respond with an `AcknowledgeUpdate` packet. Requests sent before the acknowledgement is received will be served under the old parameters. 

When the acknowledgement is received, recharge will be applied to the old parameters and then a transition will be made to the new parameters, preserving ratio of held credits to maximum credits rounding down to the nearest whole number. If an acknowledgement is not received in a timely manner (implementation specific), the remote peer is likely to be disconnected.

No updates can be made to a peer while another update remains unacknowledged.

**AcknowledgeUpdate**
[`+0x05`]

Acknowledge an update in request credit parameters. It is considered misbehavior to acknowledge an update where none has been made, or to acknowledge an update more than once. Apply the transition to the new parameters upon sending this packet.

### Transaction relay
**RelayTransactions**
[`+0x06`, [`tx_1`: `P`, `tx_2`: `P`, ...]]

Send transactions to a peer to relay to the main network.
