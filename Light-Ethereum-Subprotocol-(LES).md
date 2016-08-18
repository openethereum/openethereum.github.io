The Light Ethereum Subprotocol (LES) is the protocol used by "light" clients, which only download block headers as they appear and fetch other parts of the blockchain on-demand. They provide full functionality in terms of safely accessing the blockchain, but do not mine and therefore do not take part in the consensus process. Full and archive nodes can also support the LES protocol besides ETH in order to be able to serve light nodes.
It has been decided to create a separate sub-protocol in order to avoid interference with the consensus-critical ETH network and make it easier to update during the development phase. Some of the LES protocol messages are similar to the "new sync model" (ETH62/63) of the [Ethereum Wire Protocol](https://github.com/ethereum/wiki/wiki/Ethereum-Wire-Protocol), with the addition of a few new fields (see below).

This version of the spec may differ from the one at [Felföldi Zsolt's fork of geth](https://github.com/zsfelfoldi/go-ethereum/wiki/Light-Ethereum-Subprotocol-%28LES%29), which is the original source of this page. This version will house useful adjustments to the protocol which are eventually intended to be merged upstream so there aren't competing versions of the protocol.

### Request ID

Every on-demand request message contains a `reqID` field, which is simply returned by the server in the corresponding reply message. This helps matching replies for requests on the client side so that each reply doesn't need to be matched against each pending request.

### Client Side Flow Control

Every reply message contains a `BV` (Buffer Value) flow control feedback field, see [Client Side Flow Control](https://github.com/zsfelfoldi/go-ethereum/wiki/Client-Side-Flow-Control-model-for-the-LES-protocol)


## Messages of the LES/1 protocol version (LPV1)

### Handshake

**Status**
[`+0x00`, [`key_0`, `value_0`], [`key_1`, `value_1`], ...] Inform a peer of the sender's current LES state. This message should be sent _after_ the initial handshake and _prior_ to any LES related messages. The following keys should be present in order to be accepted by a LES/1 node: (value types are noted after the key string)

* "protocolVersion" `P`: is 1 for the LPV1 protocol version.
* "networkId" `P`: should be 0 for testnet, 1 for mainnet.
* "headTd" `P`: Total Difficulty of the best chain. Integer, as found in block header.
* "headHash" `B_32`: the hash of the best (i.e. highest TD) known block.
* "headNum" `P`: the number of the best (i.e. highest TD) known block.
* "genesisHash" `B_32`: the hash of the Genesis block.
* "serveHeaders" (no value): present if the peer can serve header chain downloads.
* "serveChainSince" `P`: present if the peer can serve Body/Receipts ODR requests starting from the given block number.
* "serveStateSince" `P`: present if the peer can serve Proof/Code ODR requests starting from the given block number.
* "txRelay" (no value): present if the peer can relay transactions to the ETH network.
* "flowControl/BL", "flowControl/MRC", "flowControl/MRR": see [Client Side Flow Control](https://github.com/zsfelfoldi/go-ethereum/wiki/Client-Side-Flow-Control-model-for-the-LES-protocol)


### Header synchronisation

**NewBlockHashes**
[`+0x01`, [`hash_0`: `B_32`, `number_0`: `P`, `td_0`: `P`], [`hash_1`: `B_32`, `number_1`: `P`, `td_1`: `P`], ...] Specify one or more new blocks which have appeared on the network. To be maximally helpful, nodes should inform peers of all blocks that they may not be aware of. Including hashes that the sending peer could reasonably be considered to know (due to the fact they were previously informed of because that node has itself advertised knowledge of the hashes through `NewBlockHashes`) is considered Bad Form, and may reduce the reputation of the sending node. Including hashes that the sending node later refuses to honour with a proceeding `GetBlockHeaders` message is considered Bad Form, and may reduce the reputation of the sending node.

**GetBlockHeaders**
[`+0x02`, `reqID`: `P`, `block`: { `P` , `B_32` }, `maxHeaders`: `P`, `skip`: `P`, `reverse`: `P` in { `0` , `1` } ] Require peer to return a `BlockHeaders` message. Reply must contain a number of block headers, of rising number when `reverse` is `0`, falling when `1`, `skip` blocks apart, beginning at block `block` (denoted by either number or hash) in the canonical chain, and with at most `maxHeaders` items.

**BlockHeaders**
[`+0x03`, `reqID`: `P`, `BV`: `P`, `blockHeader_0`, `blockHeader_1`, ...] Reply to `GetBlockHeaders`. The items in the list (following the message ID) are block headers in the format described in the main Ethereum specification, previously asked for in a `GetBlockHeaders` message. This may validly contain no block headers if no block headers were able to be returned for the `GetBlockHeaders` query.

### On-demand data retrieval

**GetBlockBodies**
[`+0x04`, `reqID`: `P`, `hash_0`: `B_32`, `hash_1`: `B_32`, ...] Require peer to return a `BlockBodies` message. Specify the set of blocks that we're interested in with the hashes.

**BlockBodies**
[`+0x05`, `reqID`: `P`, `BV`: `P`, [`transactions_0`, `uncles_0`] , ...] Reply to `GetBlockBodies`. The items in the list (following the message ID) are some of the blocks, minus the header, in the format described in the main Ethereum specification, previously asked for in a `GetBlockBodies` message.

**GetReceipts**
[`+0x06`, `reqID`: `P`, `hash_0`: `B_32`, `hash_1`: `B_32`, ...] Require peer to return a `Receipts` message.

**Receipts**
[`+0x07`, `reqID`: `P`, `BV`: `P`, [`receipt_0`, `receipt_1`], ...] Provide a set of receipts which correspond to the block hashes previously asked in `GetReceipts`.

**GetProofs**
[`+0x08`, `reqID`: `P`, [`blockhash`: `B_32`, `key`: `B_32`, `key2`: `B_32`, `fromLevel`: `P`], ...] Require peer to return a `Proofs` message, containing one or more Merkle proofs, each proving the value of index `key` from the state trie of the given block (if `key2` is empty), or the storage value of index `key2` from the storage trie referenced in the account at `key`. If `fromLevel` is greater than zero, the given number of trie nodes closest to the root can be omitted from the proof.

**Proofs**
[`+0x09`, `reqID`: `P`, `BV`: `P`, [`node_1`, `node_2`...], ...] Return a set of Merkle proofs, each consisting of a set of nodes that must be processed in order to access the trie entry value (or prove the absence of an entry) requested in `GetProofs`

**GetContractCodes**
[`+0x0a`, `reqID`: `P`, [`blockhash`: `B_32`, `key`: `B_32`], ...] Require peer to return a `ContractCodes` message.

**ContractCodes**
[`+0x0b`, `reqID`: `P`, `BV`: `P`, `value_0`: `B`, `value_1`: `B`, ...] Provide a set of contract codes which correspond to the block hashes and account keys previously asked in `GetContractCodes`.

**GetHeaderProofs**
[`+0x0d`, `reqID`: `P`, [`chtNumber`: `P`, `blockNumber`: `P`, `fromLevel`: `P`], ...] Require peer to return a `HeaderProofs` message, containing one or more canonical block headers (of block number `blockNumber`) and corresponding Merkle proofs of the CHT (Canonical Hash Trie) identified by `chtNumber`. If `fromLevel` is greater than zero, the given number of trie nodes closest to the root can be omitted from the proof.

**HeaderProofs**
[`+0x0e`, `reqID`: `P`, `BV`: `P`, [`blockHeader`, [`node_1`, `node_2`...]], ...] Return a set of structures, each containing a block header and a Merkle proof proving the header hash and belonging TD against a given CHT requested in `GetHeaderProofs`

### Transaction relaying

**SendTransactions**
[`+0x0c`, `txdata_1`, `txdata_2`, ...] Require peer to add a set of transactions into its transaction pool and relay them to the ETH network.