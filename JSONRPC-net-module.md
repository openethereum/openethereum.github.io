# The `net` Module

* [net_version](#net_version)
* [net_peerCount](#net_peercount)
* [net_listening](#net_listening)

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
