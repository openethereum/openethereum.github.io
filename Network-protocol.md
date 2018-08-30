---
title: Substrate network protocol
---

## DRAFT

Peer-to-peer communications between nodes running Polkadot clients.

Basic Chain Syncing

Two peers connect and exchange status messages. Peers are free to request any possible data ranges. However responding peer should only return reasonable amount of data that it has. If no data can be returned peer should still send an empty response message. A peer is allowed to have multiple outstanding requests as long as request `id` value is unique. 

Current protocol version: 0

## Base protocol messages

### status

Handshake message. Connecting peers must exchange status messages first. Further status messages are ignored. 

* `status` - Object.
    - `version`: `Integer` - Protocol Version. See header for current protocol version.
    - `roles`: [`Role`] - A list of roles that the node supports. `Role` can be one of the following
        - `Full` - Full client.
        - `Light` - Light client.
        - `Validator` - Validator.
        - `Collator` - Collator.
    - `best_number`: `Integer` - Best block number.
    - `best_hash`: `Hash` - Best block hash.
    - `validator_id`: `Hash` - (optional) Validator ID if validator role is supported.
    - `validator_signature`: `Signature` - (optional) Validator proof signature.
    - `parachain_id`: `Hash` - (optional) Collator parachain ID if any.

### block_request

Request block related data. Identified by block hash or number.


* `block_request` - Object.
    - `id`: `Integer` - Session unique request id.
    - `fields`: [`Field`] - A list of block data fields to request
        - `Header` - Block header.
        - `Body` - Block body.
        - `Receipt` - Block receipt.
        - `Message` - Block message queue change proof.
    - `from`: `Hash` | `Integer` - Start listing headers starting with this hash or block number.
    - `to`: `Hash` - (optional) Stop output when this hash is met
    - `direction`: `Direction` - `Ascending` or `Descending`.
    - `max`: `Integer` - Sets maximum headers in the output.


### block_response

Response to `block_request`.

* `block_response` - Object.
    - `id`: `Number` Request id for this response.
    - `blocks`: [`Block`] Block data. An object with the following fields:
        - `hash`: `Hash` - Block hash.
    	- `header`: `Bytes` - (optional) Block header.
        - `body`: `Bytes` - (optional) Block body.
        - `receipt`: `Bytes` - (optional) Block receipt.
        - `message`: `Bytes` - (optional) Messsage queue change proof.


### transactions

Propagate transactions.

* `transactions` - Object.
    - `transactions`: [`Bytes`] Transaction set.
    
### block_announce

Announce a new block.

* `block_announce` - Object.
    - `header`: `Bytes` Announced block header.

## Collator to validator messages

### para_block_request

* `para_block_request` - Object.
    - `best_hash`: `Hash` Current best block hash.

### para_block_response

* `para_block_response` - Object.
    - `block`: `Bytes` Block proposal.
    - `proof`: `Bytes` Proposal proof.
    
    
## Consensus messages

TODO:
