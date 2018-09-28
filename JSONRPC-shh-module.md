---
title: The `shh` Module
---

**Note:** _Run parity with the `--whisper` flag to enable this API._

## JSON-RPC methods

- [shh_info](#shh_info)
- [shh_newKeyPair](#shh_newKeyPair)
- [shh_addPrivateKey](#shh_addPrivateKey)
- [shh_newSymKey](#shh_newSymKey)
- [shh_addSymKey](#shh_addSymKey)
- [shh_getPublicKey](#shh_getPublicKey)
- [shh_getPrivateKey](#shh_getPrivateKey)
- [shh_getSymKey](#shh_getSymKey)
- [shh_deleteKey](#shh_deleteKey)
- [shh_post](#shh_post)
- [shh_newMessageFilter](#shh_newMessageFilter)
- [shh_getFilterMessages](#shh_getFilterMessages)
- [shh_deleteMessageFilter](#shh_deleteMessageFilter)
- [shh_subscribe](#shh_subscribe)
- [shh_unsubscribe](#shh_unsubscribe)

## JSON-RPC API Reference

### shh_info

Returns info about the whisper node.

#### Parameters

None

#### Returns

- `Object`
  - `minPow`: `float` required PoW threshold for a message to be accepted into the local pool, or null if there is empty space in the pool.
  - `messages`: `Quantity` - Number of messages in the pool.
  - `memory`: `Quantity` - Amount of memory used by messages in the pool.
  - `targetMemory`: `Quantity` - Target amount of memory for the pool.

#### Example

Request
```
curl --data '{"method":"shh_info","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":{"memory":0,"messages":0,"targetMemory":10485760},"id":1}
```


***

### shh_newKeyPair

Generate a new key pair for asymmetric encryption

#### Parameters

None

#### Returns

- `Data` 32 bytes - A unique identity to refer to this keypair by.

#### Example

Request
```
curl --data '{"method":"shh_newKeyPair","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":"0x498cc0323523a6b778bc43edbdbd4402772dfa4aaddc268ed1653e1c85f285d5","id":1}
```

### shh_addPrivateKey

Import a private key to use for asymmetric decryption.

#### Parameters

0. `Data` - 32 bytes - The private key to import

#### Returns

- `Data` - 32 bytes - A unique identity to refer to this keypair by.

#### Example


Request
```
curl --data '{"method":"shh_addPrivateKey","params":["0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":"0x2265fc77fdb16fe54260a66437267c3cf5b6929d91f7994f48191e9ad9584c50","id":1}
```


### shh_newSymKey

Generate a key for symmetric encryption

#### Parameters

None

#### Returns

- `Data` - 32 bytes - A unique identity to refer to this key by.

#### Example

Request
```
curl --data '{"method":"shh_newSymKey","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":"0x38cbc721b0a10a85134af2b2c1546850b5ef626e3ca4df37d991c527e22de61f","id":1}
```


### shh_getPublicKey

Get the public key associated with an asymmetric identity.

#### Parameters

- `Data` - 32 bytes - The identity to fetch the public key for.

#### Returns

- `Data` - 64 bytes - The public key of the asymmetric identity.

#### Example

Request
```
curl --data '{"method":"shh_getPublicKey","params":["0x3dc157405a2f8426df9e36b21fa99b36be2ae317eb09cde5e29b0a5505810561"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":"0x0b14b6056be85728b3a32918c7e067fc5f382c9d32f8893e5d161177404855c183b0fb5026c2fec382f9635f5366759075aef7474330d16613aedc43c83ad2f9","id":1}
```

### shh_getPrivateKey

Get the private key associated with an asymmetric identity.

#### Parameters

- `Data` - 32 bytes - The identity to fetch the private key for.

#### Returns

- `Data` - 32 bytes - The private key of the asymmetric identity.

#### Example

Request
```
curl --data '{"method":"shh_getPrivateKey","params":["0x3dc157405a2f8426df9e36b21fa99b36be2ae317eb09cde5e29b0a5505810561"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":"0x04aa79b13d4e9f765972e9f8207de424ddbee123a6955e21b930ae067ef22e4d","id":1}
```

### shh_getSymKey

Get the key associated with a symmetric identity.

#### Parameters

- `Data` - 32 bytes - The identity to fetch the key for.

#### Returns

- `Data` - 32 bytes - The key of the symmetric identity.

#### Example

Request
```
curl --data '{"method":"shh_getSymKey","params":["0xf9a60ba691ef4e4755143db9c76d254f9ce813b361037f6f90dc53dc71296fd8"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":"0xe4288c636477f3f7685a3112e7e3e717b78a4310366596b45161553e41f3cd56","id":1}
```

### shh_deleteKey

Delete the key or key pair denoted by the given identity.

#### Parameters

- `Data` - 32 bytes - The identity to remove.

#### Returns

- `bool` - `true` on successful removal, `false` on unknown identity.

#### Example

Request
```
curl --data '{"method":"shh_deleteKey","params":["0xf9a60ba691ef4e4755143db9c76d254f9ce813b361037f6f90dc53dc71296fd8"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":true,"id":1}
```

### shh_post

Post a message to the whisper network.

#### Parameters

- 0. `Object`
  - `to`: `Object` The receiver of the message. Can be omitted for a broadcast message. Use one of the following two fields
    - `public`: `Data` - 64 bytes - The public key of the recipient
    - `identity`: `Data` - 32 bytes - The identity of the recipient key on your local node.
  - `from` `Data` - 32 bytes - asymmetric identity to sign the message with, or null.
  - `topics`: [`Data`] - Array of topics for the message. Should be non-empty.
  - `payload`: `Data` - Message data
  - `padding`: `Data` - Optional padding. Up to 2^24 - 1 bytes.
  - `priority`: `Quantity` - How many milliseconds to spend doing PoW.
  - `ttl`: `Quantity` - Time to live (in seconds) of the message before expiry 

#### Returns

- `bool` - `true` on successful posting of the message.

#### Example

Request
```
curl --data '{"method":"shh_post","params":[{
   "from":"0x193f71c502feb0c181ed0b97352fdcebcb621c733cd80637b2154a2a2b867a12",
   "topics":["0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6"],
   "payload":"0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6",
   "priority":40,
   "ttl":400
 }],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":true,"id":1}
```

### shh_newMessageFilter

Create a new polled filter for messages.
#### Parameters

0. `Object`
  - `decryptWith`: `Data` - 32 bytes - Identity of key used for description. null if listening for broadcasts.
  - `from`: `Data` - 64 bytes - if present, only accept messages signed by this key.
  - `topics`: [`Data`] - Only accept messages matching these topics. Should be non-empty.

#### Returns

- `Data` - 32 bytes - Unique identity for this filter.

#### Example

Request
```
curl --data '{"method":"shh_newMessageFilter","params":[{"decryptWith":"0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6", "from":"0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6b10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6",
"topics":["0xb10e2d527612073b26eecdfd717e6a320cf44b4afac2b0732d9fcbe2b7fa0cf6"]}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":"0xea7120c5408c72cfd7e0e1d2ff62df8e208d9a1f85d2ed54a4a3e1ad6daeb6f9","id":1}
```


### shh_getFilterMessages

Poll for changes on a filter.

#### Parameters

- `Data` - 32 bytes - The identity to fetch changes for.

#### Returns

- `Array`, where each item an `Object`
  - `from`: `Data` - 64 bytes - Public key that signed this message or null
  - `recipient`: `Data` - 32 bytes - local identity which decrypted this message, or null if broadcast.
  - `ttl`: `Quantity` - time to live of the message in seconds.
  - `topics`: [`Data`] - Topics which matched the filter
  - `timestamp`: `Quantity` - Unix timestamp of the message
  - `payload`: `Data` - The message body
  - `padding`: `Data` - Optional padding which was decoded.

#### Example

Request
```
curl --data '{"method":"shh_getFilterMessages","params":["0x2f49562fb4a52d43297f1208296d22263a9f6fb7518ef21d29129fddbaf926ee"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":[],"id":1}
```

### shh_deleteMessageFilter

Delete a message filter by identifier

#### Parameters

- `Data` - 32 bytes - The identity of the filter to delete.

#### Returns

- `bool` - `true` on deletion, `false` on unrecognized ID.

#### Example

Request
```
curl --data '{"method":"shh_deleteMessageFilter","params":["0xea7120c5408c72cfd7e0e1d2ff62df8e208d9a1f85d2ed54a4a3e1ad6daeb6f9"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```
{"jsonrpc":"2.0","result":true,"id":1}
```

### shh_subscribe

Open a subscription to a filter. Works in general the same as `eth_subscribe` and `parity_subscribe` in pubsub.
Subscription calls are only supported on the websocket transport. ('ws://localhost:8545')

#### Parameters

See [shh_newMessageFilter](#shh_newMessageFilter)

#### Returns

subscription id

#### Example

TODO


### shh_unsubscribe

Close a subscribed filter

#### Parameters

- `Data` - The subscription ID of the filter

#### Returns

- `bool` - `true` on success, `false` on unknown subscription ID

#### Example

TODO
