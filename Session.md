---
title: Session
---

Substrate is a framework for building blockchains. It has a design with three layers including:
  * Core (essentials functionality for building a blockchain including consensus, block production, chain synchronisation, I/O for JSON-RPC, runtime, network synchronisation, database backend, telemetry, sandboxing, and versioning
  * SRML (Substrate Runtime Module Library) - extensible generic configurable module runtime system and helpful utility and library to help create runtimes and reuse the code to create others. It includes **Session Management**
  * Node
  * Peripheral Support Infrastructure

When an instance of a Substrate Client node is compiled and run, a command-line interface (CLI) becomes available and it configures and instantiates the chain, peer-to-peer networking (p2p) and synchronisation, JSON-RPC (rpc), and provides information via WebSockets to telemetry at intervals such as the quantity of peers, the synchronisation status, and the best block.

The interface of the chain instance comprises (this has changed in PoC-2):
  * "Block database" (base functionality such as open, close, empty, size, drop, del, get, put) including best block, header
  * "State database" (Trie database with a root hash and a "Transaction database" that is either Disk or Memory implementation of a Patricia Trie), for consensus, council, council voting, democracy, parachains, **session**, staking, system, and timestamp
  * Chain interface information, including list of boot nodes, and genesis configuration
  * Executor interface information, providing ability to:
    * Initialise, finalise, and generate a block. It initialises the block from a list of provided unsigned transaction outputs (UXTOs) by creating a transaction to the block database that provides a raw header (including list of extrinsics, block number, and parent hash) that is then encoded. It returns the state root after finalising and generating an encoded block (containing the extrinsics, and a header comprising the state root) 
    * Applies an extrinsic to create an instance of the WASM on-chain runtime environment (with WASM heap memory) using chain consensus bytecode retrieved from the runtime
    * Obtain chain genesis information

As shown above, "State database" contains **session** that has an interface that provides methods to get, set, and delete validators.

Within "State database" we also have staking that allows the amount of (state storage) **sessions per era** to be configured `sessionsPerEra`

In the SRML layer we store as a Node Index the **Session ID** of a node in the p2p network.

Validators submit proposals to author new blocks on a parent block hash (built from a transaction pool of extrinsics) that other validators are required to vote upon in a proposal round. 

A remote task executor starts a **session** upon creation of a new consensus gossip utility instance for the consensus **session** that is used for communicating messages between authority peers (validators) in the network for validating candidate blocks, and evaluating whether valid blocks proposed are the correct new chain head, and finalising and generating new blocks to extend the chain. The messages are obtained by the SRML from polling and are BFT message streams that are chain specific. The messages are BFT messages that are communicated amongst a list of authorities (validators that are linked to a physical entity nodes that are explicitely allowed to create new blocks and secure the blockchain) and each have a Local ID that is generated from signing the message with es25519. Each gossipped BFT consensus message that is sent is signed as a 512-bit hash using the **Substrate Authority IDs' es25519 Public Key (aka SessionKey) of the Session** to be allocated with a Local ID and voted upon by the list of authorities (validators) and its signature is checked. The validity of a proposal for a new block is evaluated on top of its parent block hash. Misbehaviour is reported by submitting a transaction to the transaction pool. Validators that were potentially offline and did not propose during a round is recorded.

The Node Executor of the runtime has a Genesis configuration that contains the following and emits a new **session** event upon finalisation of a block:
* **Session** configuration comprising a **Session Length**, and a list of Validators
* Staking configuration comprising a **Sessions Per Era, Session Reward**

In the Core layer, for JSON-RPC requests we create a persistent **Session** of metadata by using publish-subscribe (if supported by the transport protocol being used).

The runtime has an internal log with digest items that watches changes in the **Session Key (AuthorityId)**.

The runtime exposes API methods associated with a **session** such as the list of authorities (linked to a physical entity) `authorities()`, the quantity of validators `validator_count()`, and the list of validator account IDs (not linked to a physical entity) `validators()`

Note that a **runtime** is a State Transition Function (STF) of the blockchain where consensus occurs on building blocks with transactions that change the state