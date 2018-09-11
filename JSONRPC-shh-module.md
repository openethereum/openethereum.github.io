# The `shh` Module

## JSON-RPC methods

- [shh_addPrivateKey](#shh_addprivatekey)
- [shh_deleteKey](#shh_deletekey)
- [shh_deleteMessageFilter](#shh_deletemessagefilter)
- [shh_getFilterMessages](#shh_getfiltermessages)
- [shh_getPrivateKey](#shh_getprivatekey)
- [shh_getPublicKey](#shh_getpublickey)
- [shh_getSymKey](#shh_getsymkey)
- [shh_info](#shh_info)
- [shh_newKeyPair](#shh_newkeypair)
- [shh_newMessageFilter](#shh_newmessagefilter)
- [shh_newSymKey](#shh_newsymkey)
- [shh_post](#shh_post)
- [shh_subscribe](#shh_subscribe)
- [shh_unsubscribe](#shh_unsubscribe)

## JSON-RPC API Reference

### shh_addPrivateKey

Import a private key to use for asymmetric decryption.

#### Parameters

0. `Data` - 32 Bytes - The private key to import

#### Returns

- `Data` - `32 Bytes`  A unique identity to refer to this keypair by.

***

### shh_deleteKey

Delete the key or key pair denoted by the given identity.

#### Parameters

0. `Data` - `32 Bytes` - The identity to remove.

#### Returns

- `Data` - `true` on successful removal, `false` on unknown identity

***

### shh_deleteMessageFilter

Delete a message filter by identifier

#### Parameters

0. `Data` - `32 bytes` - The identity of the filter to delete.

#### Returns

- `Boolean` - `true` on deletion, `false` on unrecognized ID.

***

### shh_getFilterMessages

Polling method for whisper filters. Returns new messages since the last call of this method.
**Note** calling the [shh_getMessages](#shh_getmessages) method, will reset the buffer for this method, so that you won't receive duplicate messages.

#### Parameters

0. `Data` - `32 bytes` - Unique identity to fetch changes for.

#### Returns

- `Array` - Array of `messages` received since last poll
    - `from`: `Data` - `64 bytes` - Public key that signed this message or null
    - `recipient`: `Data` - `32 bytes` - local identity which decrypted this message, or null if broadcast.
    - `ttl`: `Quantity` - time to live of the message in seconds.
    - `topics`: `Array` - Array of `Data` - Topics which matched the filter
    - `timestamp`: `Quantity` - Unix timestamp of the message
    - `payload`: `Data` - The message body
    - `padding`: `Data` - Optional padding which was decoded.

***

### shh_getPrivateKey

Get the private key associated with an asymmetric identity.

#### Parameters

0. `Data` - 32 Bytes - The identity to fetch the private key for.

#### Returns

- `Data` - `32 Bytes` - The private key of the asymmetric identity.

***

### shh_getPublicKey

Get the public key associated with an asymmetric identity.

#### Parameters

0. `Data` - 32 Bytes - The identity to fetch the public key for.

#### Returns

- `Data` - `64 Bytes` - The public key of the asymmetric identity.

***

### shh_getSymKey

Get the key associated with a symmetric identity.

#### Parameters

0. `Data` - `32 Bytes` - The identity to fetch the key for.

#### Returns

- `Data` - `64 Bytes` - The key of the asymmetric identity.

***

### shh_info

Returns the current whisper protocol version.

#### Parameters

None

#### Returns

- `Object` - The current whisper protocol version
    - `minPow`: `Float` - required PoW threshold for a message to be accepted into the local pool, or null if there is empty space in the pool.
    - `messages`: `Quantity` - Number of messages in the pool.
    - `memory`: `Quantity` - Amount of memory used by messages in the pool.
    - `targetMemory`: `Quantity` - Target amount of memory for the pool.

#### Example

Request
```bash
curl --data '{"method":"shh_info","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

***

### shh_newKeyPair

Generate a new key pair (identity) for asymmetric encryption.

#### Parameters

None

#### Returns

- `Data` - 32 Bytes - the address of the new identiy

#### Example

Request
```bash
curl --data '{"method":"shh_newKeyPair","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

***

### shh_newMessageFilter

Create a new polled filter for messages.

#### Parameters

0. `Object` - The filter options:
    - `decryptWith`: `Data` - `32 bytes` - Identity of key used for description. null if listening for broadcasts.
    - `from`: `Data` - (optional) `32 Bytes` - if present, only accept messages signed by this key.
    - `topics`: `Array` - Array of `Data`. Only accept messages matching these topics. Should be non-empty.

#### Returns

- `Data` - `32 bytes` - Unique identity for this filter.

***

### shh_newSymKey

Generate a key pair(identity) for symmetric encryption.

#### Parameters

None

#### Returns

- `Data` - 32 Bytes - the address of the new identiy

#### Example

Request
```bash
curl --data '{"method":"shh_newSymKey","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

***

### shh_post

Sends a whisper message.

#### Parameters

0. `Object` - The whisper post object:
    - `from`: `Data` - (optional) 60 Bytes - The identity of the sender
    - `to`: `Data` - (optional) 60 Bytes - The identity of the receiver. When present whisper will encrypt the message so that only the receiver can decrypt it
    - `topics`: `Array` - Array of `Data` topics, for the receiver to identify messages
    - `payload`: `Data` - The payload of the message
    - `priority`: `Quantity` - The integer of the priority in a rang from ... (?)
    - `ttl`: `Quantity` - Integer of the time to live in seconds.

#### Returns

- `Boolean` - `true` if the message was send, otherwise `false`

***

### shh_subscribe

Open a subscription to a filter.

#### Parameters

0. `Data` - See [shh_newMessageFilter](#shh_newmessagefilter)

#### Returns

- `Quantity` - Unique subscription identifier

***

### shh_unsubscribe

Close a subscribed filter

#### Parameters

0. `Quantity` - Unique subscription identifier

#### Returns

- `Boolean` - `true` on success, `false` on unknown subscription ID.

