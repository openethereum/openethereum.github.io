---
title: The `net` Module
---

## JSON-RPC methods

- [net_listening](#net_listening)
- [net_peerCount](#net_peercount)
- [net_version](#net_version)

## JSON-RPC API Reference

### net_listening

Returns `true` if client is actively listening for network connections.

#### Parameters

None

#### Returns

- `Boolean` - `true` when listening, otherwise `false`.

#### Example

Request
```bash
curl --data '{"method":"net_listening","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

### net_peerCount

Returns number of peers currenly connected to the client.

#### Parameters

None

#### Returns

- `Quantity` - Integer of the number of connected peers

#### Example

Request
```bash
curl --data '{"method":"net_peerCount","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x2" // 2
}
```

***

### net_version

Returns the current network protocol version.

#### Parameters

None

#### Returns

- `String` - The current network protocol version

#### Example

Request
```bash
curl --data '{"method":"net_version","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "8995"
}
```
