---
title: JSON RPC API
---

[JSON](http://json.org/) is a lightweight data-interchange format. It can represent numbers, strings, ordered sequences of values, and collections of name/value pairs.

[JSON-RPC](http://www.jsonrpc.org/specification) is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over HTTP, or in many various message passing environments. It uses JSON ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) as data format.

## Transport
* HTTP: Listens on port `8545`
* WebSockets: Listens on port `8546`
* IPC Socket: Listens on `$BASE/jsonrpc.ipc` (defaults to `~/.local/share/openethereum/jsonrpc.ipc` on Linux)

## Types in the JSONRPC

There are several datatypes that are passed over JSON. Of these two are "special" types used particularly for the Ethereum API since Javascript/JSON has no native means of encoding unformatted byte arrays nor large quantities. Both are passed with a hex encoding, however with different requirements to formatting:

When encoding **QUANTITIES** (integers, numbers): encode as hex, prefix with "0x", the most compact representation (slight exception: zero should be represented as "0x0"). Examples:
- 0x41 (65 in decimal)
- 0x400 (1024 in decimal)
- WRONG: 0x (should always have at least one digit - zero is "0x0")
- WRONG: 0x0400 (no leading zeroes allowed)
- WRONG: ff (must be prefixed 0x)

When encoding **UNFORMATTED DATA** (byte arrays, account addresses, hashes, bytecode arrays): encode as hex, prefix with "0x", two hex digits per byte. Examples:
- 0x41 (size 1, "A")
- 0x004200 (size 3, "\0B\0")
- 0x (size 0, "")
- WRONG: 0xf0f0f (must be even number of digits)
- WRONG: 004200 (must be prefixed 0x)

Aside from `DATA` and `QUANTITIES`, there are also normative JSON types, which we describe as `STR` (strings) and `INT` (integer numbers).

## Objects in the JSONRPC

There are multiple objects in JSONRPC that are repeating itself throughout the spec.

### Transactions

Depending on the type of the transaction, the object can be:
- Legacy transaction, the only transaction available before EIP-2718. It contains fields:
    - `from`:   `Address` - 20 Bytes - The address the transaction is sent from.
    - `to`:   `Address` - (optional when creating new contract) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`:   `Quantity` - (optional) Integer of a nonce. This allows you to overwrite your own pending transactions that use the same nonce.
- AccessList transaction, EIP-2930: Contains all fields from a legacy transaction in addition to:
  - `type`:   `0x1` - transaction type, hardcoded.
  - `accessList`:   `List` - of objects
    - `address`:   `Address` - 20 Bytes - address that will be accessed
    - `storageKeys`:   `List` - of 32 Byte items - storage keys that will be accessed

Additional field that can be found in the transaction object for a few rpc calls:
  - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

### Transaction Responses
Depending on the transaction type of transaction response object can be:
- Legacy transaction is only transaction available before EIP-2718. It contain fields:
    - `hash`:   `Hash` - 32 Bytes - hash of the transaction.
    - `nonce`:   `Quantity` - the number of transactions made by the sender prior to this one.
    - `blockHash`:   `Hash` - 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
    - `blockNumber`:   `Quantity` or `Tag` - block number where this transaction was in. `null` when its pending.
    - `transactionIndex`:   `Quantity` - integer of the transactions index position in the block. `null` when its pending.
    - `from`:   `Address` - 20 Bytes - address of the sender.
    - `to`:   `Address` - 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
    - `value`:   `Quantity` - value transferred in Wei.
    - `gasPrice`:   `Quantity` - gas price provided by the sender in Wei.
    - `gas`:   `Quantity` - gas provided by the sender.
    - `input`:   `Data` - the data send along with the transaction.
    - `v`:   `Quantity` - the standardised V field of the signature.
    - `standardV`:   `Quantity` - the standardised V field of the signature (0 or 1).
    - `r`:   `Quantity` - the R field of the signature.
    - `raw`:   `Data` - raw transaction data
    - `publicKey`:   `Hash` - public key of the signer.
    - `chainId`:   `Quantity` - the chain id of the transaction, if any.
    - `creates`:   `Hash` - creates contract hash
    - `condition`:   `Object` - (optional) conditional submission, Block number in `block` or timestamp in `time` or `null`.
- AccessList transaction, EIP-2930: Contains all field from legacy transaction but with removed `standardV` (`standardV` is ommited because `v` in spec is decupled from `chainId` and represent parityV/`standardV` from signature) and with addition fields:
  - `type`:   `"0x1"` - transaction type, hardcoded.
  - `accessList`:   `List` - of objects
    - `address`:   `Address` - 20 Bytes - address that will be accessed
    - `storageKeys`:   `List` - of 32 Byte items - storage keys that will be accessed

### Receipts

Depending on the type of transaction we have different receipt types:
- Legacy receipt is only receipt available before EIP-2718. It contain fields:
    - `blockHash`:   `Hash` - 32 Bytes - hash of the block where this transaction was in.
    - `blockNumber`:   `Quantity` or `Tag` - block number where this transaction was in.
    - `contractAddress`:   `Address` - 20 Bytes - The contract address created, if the transaction was a contract creation, otherwise `null`.
    - `cumulativeGasUsed`:   `Quantity` - The total amount of gas used when this transaction was executed in the block.
    - `from`:   `Address` - 20 Bytes - The address of the sender.
    - `to`:   `Address` - 20 Bytes - The address of the receiver. `null` when it’s a contract creation transaction.
    - `gasUsed`:   `Quantity` - The amount of gas used by this specific transaction alone.
    - `logs`:   `Array` - Array of log objects, which this transaction generated.
    - `logsBloom`:   `Hash` - 256 Bytes - A bloom filter of logs/events generated by contracts during transaction execution. Used to efficiently rule out transactions without expected logs.
    - `root`:   `Hash` - 32 Bytes - Merkle root of the state trie after the transaction has been executed (optional after Byzantium hard fork [EIP609](https://eips.ethereum.org/EIPS/eip-609))
    - `status`:   `Quantity` - `0x0` indicates transaction failure , `0x1` indicates transaction success. Set for blocks mined after Byzantium hard fork [EIP609](https://eips.ethereum.org/EIPS/eip-609), `null` before.
    - `transactionHash`:   `Hash` - 32 Bytes - hash of the transaction.
    - `transactionIndex`:   `Quantity` - Integer of the transactions index position in the block.
- AccessList receipt, EIP-2930: Contains all field from legacy receipt with addition of:
  - `type:`   `0x1` - receipt type, hardcoded



## Enabled APIs (default)
As documented in the options, available under `openethereum --help` not all API's are exposed by default. However you can simply enable them by running OpenEthereum with the flag: 
`--jsonrpc-apis APIS `                  
> _Specify the APIs available through the JSONRPC interface. APIS is a comma-delimited list of API name. Possible name are all, safe, web3, eth, net, personal, parity, parity_set, traces, rpc, parity_accounts, signer. You can also disable a specific API by putting '-' in the front: all,-personal._

**default**: web3,eth,pubsub,net,parity,parity_pubsub,traces,rpc,secretstore.

## Full API Documentation

- [web3](JSONRPC-web3-module)
- [net](JSONRPC-net-module)
- [eth](JSONRPC-eth-module)
- [eth_pubsub](JSONRPC-eth_pubsub-module)
- [personal](JSONRPC-personal-module)
- [parity](JSONRPC-parity-module)
- [parity_accounts](JSONRPC-parity_accounts-module)
- [parity_set](JSONRPC-parity_set-module)
- [pubsub](JSONRPC-parity_pubsub-module)
- [signer](JSONRPC-signer-module)
- [trace](JSONRPC-trace-module)
- [secretstore](JSONRPC-secretstore-module)
