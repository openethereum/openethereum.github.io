---
title: The `web3` Module
---

## JSON-RPC methods

- [web3_clientVersion](#web3_clientversion)
- [web3_sha3](#web3_sha3)

## JSON-RPC API Reference

### web3_clientVersion

Returns the current client version.

#### Parameters

None

#### Returns

- `String` - The current client version

#### Example

Request
```bash
curl --data '{"method":"web3_clientVersion","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "Parity//v1.5.0-unstable-9db3f38-20170103/x86_64-linux-gnu/rustc1.14.0"
}
```

***

### web3_sha3

Returns Keccak-256 (**not** the standardized SHA3-256) of the given data.

#### Parameters

0. `String` - The data to convert into a SHA3 hash.

```js
params: [
  "0x68656c6c6f20776f726c64" // "hello world"
]
```

#### Returns

- `Data` - The Keccak-256 hash of the given string.

#### Example

Request
```bash
curl --data '{"method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```
