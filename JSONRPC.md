---
title: JSON RPC API
---

[JSON](http://json.org/) is a lightweight data-interchange format. It can represent numbers, strings, ordered sequences of values, and collections of name/value pairs.

[JSON-RPC](http://www.jsonrpc.org/specification) is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over HTTP, or in many various message passing environments. It uses JSON ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) as data format.

## Transport
* HTTP: Listens on port `8545`
* WebSockets: Listens on port `8546`
* IPC Socket: Listens on `$BASE/jsonrpc.ipc` (defaults to `~/.local/share/io.parity.ethereum/jsonrpc.ipc` on Linux)

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

## Enabled APIs (default)
As documented in the options, available under `parity --help` not all API's are exposed by default. However you can simply enable them by running parity with the flag: 
`--jsonrpc-apis APIS `                  
> _Specify the APIs available through the JSONRPC interface. APIS is a comma-delimited list of API name. Possible name are all, safe, web3, eth, net, personal, parity, parity_set, traces, rpc, parity_accounts, signer. You can also disable a specific API by putting '-' in the front: all,-personal._

**default**: web3,eth,pubsub,net,parity,parity_pubsub,traces,rpc,secretstore.

## Full API Documentation

- [web3](JSONRPC-web3-module.md)
- [net](JSONRPC-net-module.md)
- [eth](JSONRPC-eth-module.md)
- [eth_pubsub](JSONRPC-Eth-Pub-Sub-Module.md)
- [personal](JSONRPC-personal-module.md)
- [parity](JSONRPC-parity-module.md)
- [parity_accounts](JSONRPC-parity_accounts-module.md)
- [parity_set](JSONRPC-parity_set-module.md)
- [pubsub](JSONRPC-Parity-Pub-Sub-module.md)
- [signer](JSONRPC-signer-module.md)
- [trace](JSONRPC-trace-module.md)
- [shh](JSONRPC-shh-Module.md)
- [secretstore](JSONRPC-secretstore-module.md)
