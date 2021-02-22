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

### Transaction

Depending on the type of transaction object can be:
- Legacy transaction is only transaction available before EIP-2718. It contain fields:
    - `from`:   `Address` - 20 Bytes - The address the transaction is send from.
    - `to`:   `Address` - (optional when creating new contract) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`:   `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
- AccessList transaction, EIP-2930: Contains all field from legacy transaction with addition of:
  - `type`: `0x1` transaction type.
  - `accessList`: `List` of objects
    - `address`: 20 Bytes - address that will be accessed
    - `storageKeys`: `List` of 32 Bytes items - storage keys that will be accessed

Additional field that can be found in transaction object for few rpc calls:
  - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

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
