# The `web3` Module

* [web3_clientVersion](#web3_clientversion)
* [web3_sha3](#web3_sha3)

***

## JSON RPC API Reference

#### web3_clientVersion

Returns the current client version.

##### Parameters
none

##### Returns

`String` - The current client version

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}' localhost:8545

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "Mist/v0.9.3/darwin/go1.4.1"
}
```

***

#### web3_sha3

Returns Keccak-256 (*not* the standardized SHA3-256) of the given data.

##### Parameters

1. `String` - the data to convert into a SHA3 hash

```js
params: [
  '0x68656c6c6f20776f726c64'
]
```

##### Returns

`DATA` - The SHA3 result of the given string.

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":1}' localhost:8545

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```

# The `net` Module

* [net_version](#net_version)
* [net_peerCount](#net_peercount)
* [net_listening](#net_listening)

***

## JSON RPC API Reference

#### net_version

Returns the current network protocol version.

##### Parameters
none

##### Returns

`String` - The current network protocol version

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":1}' localhost:8545

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "59"
}
```

***

#### net_listening

Returns `true` if client is actively listening for network connections.

##### Parameters
none

##### Returns

`Boolean` - `true` when listening, otherwise `false`.

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":67} localhost:8545

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result":true
}
```

***

#### net_peerCount

Returns number of peers currenly connected to the client.

##### Parameters
none

##### Returns

`QUANTITY` - integer of the number of connected peers.

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":74}' localhost:8545

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": "0x2" // 2
}
```

***
