---
title: The `parity_set` Module
---

## JSON-RPC methods

- [parity_acceptNonReservedPeers](#parity_acceptnonreservedpeers)
- [parity_addReservedPeer](#parity_addreservedpeer)
- [parity_dropNonReservedPeers](#parity_dropnonreservedpeers)
- [parity_executeUpgrade](#parity_executeupgrade)
- [parity_hashContent](#parity_hashcontent)
- [parity_removeReservedPeer](#parity_removereservedpeer)
- [parity_setAuthor](#parity_setauthor)
- [parity_setChain](#parity_setchain)
- [parity_setEngineSigner](#parity_setenginesigner)
- [parity_setExtraData](#parity_setextradata)
- [parity_setGasCeilTarget](#parity_setgasceiltarget)
- [parity_setGasFloorTarget](#parity_setgasfloortarget)
- [parity_setMaxTransactionGas](#parity_setmaxtransactiongas)
- [parity_setMinGasPrice](#parity_setmingasprice)
- [parity_setMode](#parity_setmode)
- [parity_setTransactionsLimit](#parity_settransactionslimit)
- [parity_upgradeReady](#parity_upgradeready)

## JSON-RPC API Reference

### parity_acceptNonReservedPeers

Set Parity to accept non-reserved peers (default behavior).

#### Parameters

None

#### Returns

- `Boolean` - `true` if successful.

#### Example

Request
```bash
curl --data '{"method":"parity_acceptNonReservedPeers","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_addReservedPeer

Add a reserved peer.

#### Parameters

0. `String` - Enode address

```js
params: ["enode://a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c@22.99.55.44:7770"]
```

#### Returns

- `Boolean` - `true` if successful.

#### Example

Request
```bash
curl --data '{"method":"parity_addReservedPeer","params":["enode://a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c@22.99.55.44:7770"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_dropNonReservedPeers

Set Parity to drop all non-reserved peers. To restore default behavior call [parity_acceptNonReservedPeers](#parity_acceptnonreservedpeers).

#### Parameters

None

#### Returns

- `Boolean` - `true` if successful.

#### Example

Request
```bash
curl --data '{"method":"parity_dropNonReservedPeers","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_executeUpgrade

Attempts to upgrade Parity to the version specified in [parity_upgradeReady](#parity_upgradeready).

#### Parameters

None

#### Returns

- `Boolean` - returns `true` if the upgrade to the new release was successfully executed, `false` if not.

#### Example

Request
```bash
curl --data '{"method":"parity_executeUpgrade","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_hashContent

Creates a hash of a file at a given URL.

#### Parameters

0. `String` - The url of the content.

```js
params: ["https://raw.githubusercontent.com/paritytech/parity-ethereum/master/README.md"]
```

#### Returns

- `Hash` - The SHA-3 hash of the content.

#### Example

Request
```bash
curl --data '{"method":"parity_hashContent","params":["https://raw.githubusercontent.com/paritytech/parity-ethereum/master/README.md"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x2547ea3382099c7c76d33dd468063b32d41016aacb02cbd51ebc14ff5d2b6a43"
}
```

***

### parity_removeReservedPeer

Remove a reserved peer.

#### Parameters

0. `String` - Encode address

```js
params: ["enode://a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c@22.99.55.44:7770"]
```

#### Returns

- `Boolean` - `true` if successful.

#### Example

Request
```bash
curl --data '{"method":"parity_removeReservedPeer","params":["enode://a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c@22.99.55.44:7770"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setAuthor

Changes author (coinbase) for mined blocks.

#### Parameters

0. `Address` - 20 Bytes - Address

```js
params: ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_setAuthor","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setChain

Sets the network spec file Parity is using.

#### Parameters

0. `String` - Chain spec name, one of: "foundation", "ropsten", "morden", "kovan", "classic", "dev", "expanse", "musicoin" or a filename.

```js
params: ["foundation"]
```

#### Returns

- `Boolean` - `true` if the call succeeded.

#### Example

Request
```bash
curl --data '{"method":"parity_setChain","params":["foundation"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setEngineSigner

Sets an authority account for signing consensus messages. For more information check the [Proof of Authority Chains](Proof-of-Authority-Chains.md) page.

#### Parameters

0. `Address` - Identifier of a valid authority account.
0. `String` - Passphrase to unlock the account.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "hunter2"
]
```

#### Returns

- `Boolean` - True if the call succeeded

#### Example

Request
```bash
curl --data '{"method":"parity_setEngineSigner","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setExtraData

Changes extra data for newly mined blocks

#### Parameters

0. `Data` - Extra Data

```js
params: ["0x"]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
curl --data '{"method":"parity_setExtraData","params":["0x"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setGasCeilTarget

Sets new gas ceiling target for mined blocks.

#### Parameters

0. `Quantity` - (default: `0x0`) Gas ceiling target.

```js
params: [
  "0x2540be400" // 10000000000
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_setGasCeilTarget","params":["0x2540be400"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setGasFloorTarget

Sets a new gas floor target for mined blocks..

#### Parameters

0. `Quantity` - (default: `0x0`) Gas floor target.

```js
params: [
  "0x3e8" // 1000
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_setGasFloorTarget","params":["0x3e8"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setMaxTransactionGas

Sets the maximum amount of gas a single transaction may consume.

#### Parameters

0. `Quantity` - Gas amount

```js
params: [
  "0x186a0" // 100000
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_setMaxTransactionGas","params":["0x186a0"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setMinGasPrice

Changes minimal gas price for transaction to be accepted to the queue.

#### Parameters

0. `Quantity` - Minimal gas price

```js
params: [
  "0x3e8" // 1000
]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
curl --data '{"method":"parity_setMinGasPrice","params":["0x3e8"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setMode

Changes the operating mode of Parity.

#### Parameters

0. `String` - The mode to set, one of:
  * `"active"` - Parity continuously syncs the chain.
  * `"passive"` - Parity syncs initially, then sleeps and wakes regularly to resync.
  * `"dark"` - Parity syncs only when the RPC is active.
  * `"offline"` - Parity doesn't sync.


```js
params: ["passive"]
```

#### Returns

- `Boolean` - `true` if the call succeeded.

#### Example

Request
```bash
curl --data '{"method":"parity_setMode","params":["passive"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setTransactionsLimit

Changes limit for transactions in queue.

#### Parameters

0. `Quantity` - New Limit

```js
params: [
  "0x3e8" // 1000
]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
curl --data '{"method":"parity_setTransactionsLimit","params":["0x3e8"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_upgradeReady

Returns a ReleaseInfo object describing the release which is available for upgrade or `null` if none is available.

#### Parameters

None

#### Returns

- `Object` - Details or `null` if no new release is available.
    - `version`:   `Object` - Information on the version.
    - `is_critical`:   `Boolean` - Does this release contain critical security updates?
    - `fork`:   `Quantity` - The latest fork that this release can handle.
    - `binary`:   `Data` - (optional) Keccak-256 checksum of the release parity binary, if known.

#### Example

Request
```bash
curl --data '{"method":"parity_upgradeReady","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": null
}
```

