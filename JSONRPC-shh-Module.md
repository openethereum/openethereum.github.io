# The `shh` Module

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

TODO

***

### shh_newKeyPair

Generate a new key pair for asymmetric encryption

#### Parameters

None

#### Returns

- `Data` 32 bytes - A unique identity to refer to this keypair by.

#### Example

TODO

### shh_addPrivateKey

Import a private key to use for asymmetric decryption.

#### Parameters

0. `Data` - 32 bytes - The private key to import

#### Returns

- `Data` - 32 bytes - A unique identity to refer to this keypair by.

#### Example

TODO

### shh_newSymKey

Generate a key pair for symmetric encryption

#### Parameters

None

#### Returns

- `Data` - 32 bytes - A unique identity to refer to this key by.

#### Example

TODO

### shh_getPublicKey

Get the public key associated with an asymmetric identity.

#### Parameters

- `Data` - 32 bytes - The identity to fetch the public key for.

#### Returns

- `Data` - 64 bytes - The public key of the asymmetric identity.

#### Example

TODO

### shh_getPrivateKey

Get the private key associated with an asymmetric identity.

#### Parameters

- `Data` - 32 bytes - The identity to fetch the private key for.

#### Returns

- `Data` - 32 bytes - The private key of the asymmetric identity.

#### Example

TODO

### shh_getSymKey

Get the key associated with a symmetric identity.

#### Parameters

- `Data` - 32 bytes - The identity to fetch the key for.

#### Returns

- `Data` - 64 bytes - The key of the symmetric identity.

#### Example

TODO

### shh_deleteKey

Delete the key or key pair denoted by the given identity.

#### Parameters

- `Data` - 32 bytes - The identity to remove.

#### Returns

- `bool` - `true` on successful removal, `false` on unknown identity.

#### Example

TODO

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

TODO

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

TODO


### shh_getFilterChanges

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

TODO

### shh_deleteMessageFilter

Delete a message filter by identifier

#### Parameters

- `Data` - 32 bytes - The identity of the filter to delete.

#### Returns

- `bool` - `true` on deletion, `false` on unrecognized ID.

#### Example

TODO

### shh_subscribe

Open a subscription to a filter

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