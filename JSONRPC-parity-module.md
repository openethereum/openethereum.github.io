---
title: The `parity` Module
---

## JSON-RPC methods

- [parity_cidV0](#parity_cidv0)
- [parity_composeTransaction](#parity_composetransaction)
- [parity_consensusCapability](#parity_consensuscapability)
- [parity_decryptMessage](#parity_decryptmessage)
- [parity_encryptMessage](#parity_encryptmessage)
- [parity_futureTransactions](#parity_futuretransactions)
- [parity_getBlockHeaderByNumber](#parity_getblockheaderbynumber)
- [parity_listOpenedVaults](#parity_listopenedvaults)
- [parity_listStorageKeys](#parity_liststoragekeys)
- [parity_listVaults](#parity_listvaults)
- [parity_localTransactions](#parity_localtransactions)
- [parity_releasesInfo](#parity_releasesinfo)
- [parity_signMessage](#parity_signmessage)
- [parity_versionInfo](#parity_versioninfo)

#### Account Vaults
- [parity_changeVault](#parity_changevault)
- [parity_changeVaultPassword](#parity_changevaultpassword)
- [parity_closeVault](#parity_closevault)
- [parity_getVaultMeta](#parity_getvaultmeta)
- [parity_newVault](#parity_newvault)
- [parity_openVault](#parity_openvault)
- [parity_setVaultMeta](#parity_setvaultmeta)

#### Accounts (read-only) and Signatures
- [parity_accountsInfo](#parity_accountsinfo)
- [parity_checkRequest](#parity_checkrequest)
- [parity_defaultAccount](#parity_defaultaccount)
- [parity_generateSecretPhrase](#parity_generatesecretphrase)
- [parity_hardwareAccountsInfo](#parity_hardwareaccountsinfo)
- [parity_listAccounts](#parity_listaccounts)
- [parity_phraseToAddress](#parity_phrasetoaddress)
- [parity_postSign](#parity_postsign)
- [parity_postTransaction](#parity_posttransaction)

#### Block Authoring (aka "mining")
- [parity_defaultExtraData](#parity_defaultextradata)
- [parity_extraData](#parity_extradata)
- [parity_gasCeilTarget](#parity_gasceiltarget)
- [parity_gasFloorTarget](#parity_gasfloortarget)
- [parity_minGasPrice](#parity_mingasprice)
- [parity_transactionsLimit](#parity_transactionslimit)

#### Development
- [parity_devLogs](#parity_devlogs)
- [parity_devLogsLevels](#parity_devlogslevels)

#### Network Information
- [parity_chain](#parity_chain)
- [parity_chainId](#parity_chainid)
- [parity_chainStatus](#parity_chainstatus)
- [parity_gasPriceHistogram](#parity_gaspricehistogram)
- [parity_netChain](#parity_netchain)
- [parity_netPeers](#parity_netpeers)
- [parity_netPort](#parity_netport)
- [parity_nextNonce](#parity_nextnonce)
- [parity_pendingTransactions](#parity_pendingtransactions)
- [parity_pendingTransactionsStats](#parity_pendingtransactionsstats)
- [parity_registryAddress](#parity_registryaddress)
- [parity_removeTransaction](#parity_removetransaction)
- [parity_rpcSettings](#parity_rpcsettings)
- [parity_unsignedTransactionsCount](#parity_unsignedtransactionscount)

#### Node Settings
- [parity_dappsUrl](#parity_dappsurl)
- [parity_enode](#parity_enode)
- [parity_mode](#parity_mode)
- [parity_nodeKind](#parity_nodekind)
- [parity_nodeName](#parity_nodename)
- [parity_wsUrl](#parity_wsurl)

## JSON-RPC API Reference

### parity_cidV0

Compute a v0 IPFS Content ID from protobuf encoded bytes.

#### Parameters

0. `Data` - to encode.

```js
params: ["0x666f6f626172"]
```

#### Returns

- `String` - Base58 encoded CID

#### Example

Request
```bash
curl --data '{"method":"parity_cidV0","params":["0x666f6f626172"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "QmSbFjqjd6nFwNHqsBCC7SK8GShGcayLUEtysJjNGhZAnC"
}
```

***

### parity_composeTransaction

Given partial transaction request produces transaction with all fields filled in. Such transaction can be then signed externally.

#### Parameters

0. `Object` - see [`eth_sendTransaction`](JSONRPC-eth-module#eth_sendtransaction).
    - `from`: `Address` - 20 Bytes - The address the transaction is send from.
    - `to`: `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`: `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`: `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`: `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`: `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`: `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`: `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

```js
params: [{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
  "value": "0x9184e72a" // 2441406250
}]
```

#### Returns

- `Object` - Transaction object (same as the parameter) with missing optional fields filled in by defaults.

#### Example

Request
```bash
curl --data '{"method":"parity_composeTransaction","params":[{"from":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","to":"0xd46e8dd67c5d32be8058bb8eb970870f07244567","value":"0x9184e72a"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "condition": null,
    "data": "0x",
    "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
    "gas": "0xe57e0",
    "gasPrice": "0x4a817c800",
    "nonce": "0x0",
    "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
    "value": "0x9184e72a"
  }
}
```

***

### parity_consensusCapability

Returns information on current consensus capability.

#### Parameters

None

#### Returns

- `Object` - or `String` - Either `"capable"`, `{"capableUntil":N}`, `{"incapableSince":N}` or `"unknown"` (`N` is a block number).

#### Example

Request
```bash
curl --data '{"method":"parity_consensusCapability","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "capable"
}
```

***

### parity_decryptMessage

Decrypt a message encrypted with a ECIES public key.

#### Parameters

0. `Address` - Account which can decrypt the message.
0. `Data` - Encrypted message.

```js
params: [
  "0x00a329c0648769a73afac7f9381e08fb43dbea72",
  "0x0405afee7fa2ab3e48c27b00d543389270cb7267fc191ca1311f297255a83cbe8d77a4ba135b51560700a582924fa86d2b19029fcb50d2b68d60a7df1ba81df317a19c8def117f2b9cf8c2618be0e3f146a5272fb9e5528719d2d7a1bd91fa620901cffa756305c79c093e7af30fa3c1587029421351c34a7c1e5a2b"
]
```

#### Returns

- `Data` - Decrypted message.

#### Example

Request
```bash
curl --data '{"method":"parity_decryptMessage","params":["0x00a329c0648769a73afac7f9381e08fb43dbea72","0x0405afee7fa2ab3e48c27b00d543389270cb7267fc191ca1311f297255a83cbe8d77a4ba135b51560700a582924fa86d2b19029fcb50d2b68d60a7df1ba81df317a19c8def117f2b9cf8c2618be0e3f146a5272fb9e5528719d2d7a1bd91fa620901cffa756305c79c093e7af30fa3c1587029421351c34a7c1e5a2b"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x68656c6c6f20776f726c64" // hello world
}
```

***

### parity_encryptMessage

Encrypt some data with a public key under ECIES.

#### Parameters

0. `Hash` - Public EC key generated with `secp256k1` curve, truncated to the last 64 bytes.
0. `Data` - The message to encrypt.

```js
params: [
  "0xD219959D466D666060284733A80DDF025529FEAA8337169540B3267B8763652A13D878C40830DD0952639A65986DBEC611CF2171A03CFDC37F5A40537068AA4F",
  "0x68656c6c6f20776f726c64" // "hello world"
]
```

#### Returns

- `Data` - Encrypted message.

#### Example

Request
```bash
curl --data '{"method":"parity_encryptMessage","params":["0xD219959D466D666060284733A80DDF025529FEAA8337169540B3267B8763652A13D878C40830DD0952639A65986DBEC611CF2171A03CFDC37F5A40537068AA4F","0x68656c6c6f20776f726c64"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x0491debeec5e874a453f84114c084c810708ebcb553b02f1b8c05511fa4d1a25fa38eb49a32c815e2b39b7bcd56d66648bf401067f15413dae683084ca7b01e21df89be9ec4bc6c762a657dbd3ba1540f557e366681b53629bb2c02e1443b5c0adc6b68f3442c879456d6a21ec9ed07847fa3c3ecb73ec7ee9f8e32d"
}
```

***

### parity_futureTransactions

Returns all future transactions from transaction queue.

#### Parameters

None

#### Returns

- `Array` - Transaction list.
    - `hash`: `Hash` - 32 Bytes - hash of the transaction.
    - `nonce`: `Quantity` - The number of transactions made by the sender prior to this one.
    - `blockHash`: `Hash` - 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
    - `blockNumber`: `Quantity` | `Tag` - Block number where this transaction was in. `null` when its pending.
    - `transactionIndex`: `Quantity` - Integer of the transactions index position in the block. `null` when its pending.
    - `from`: `Address` - 20 Bytes - address of the sender.
    - `to`: `Address` - 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
    - `value`: `Quantity` - Value transferred in Wei.
    - `gasPrice`: `Quantity` - Gas price provided by the sender in Wei.
    - `gas`: `Quantity` - Gas provided by the sender.
    - `input`: `Data` - The data send along with the transaction.
    - `creates`: `Address` - (optional) Address of a created contract or `null`.
    - `raw`: `Data` - Raw transaction data.
    - `publicKey`: `Data` - Public key of the signer.
    - `networkId`: `Quantity` - The network id of the transaction, if any.
    - `standardV`: `Quantity` - The standardized V field of the signature (0 or 1).
    - `v`: `Quantity` - The V field of the signature.
    - `r`: `Quantity` - The R field of the signature.
    - `s`: `Quantity` - The S field of the signature.
    - `condition`: `Object` - (optional) Conditional submission, Block number in `block` or timestamp in `time` or `null`.

#### Example

Request
```bash
curl --data '{"method":"parity_futureTransactions","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    {
      "hash": "0x80de421cd2e7e46824a91c343ca42b2ff339409eef09e2d9d73882462f8fce31",
      "nonce": "0x1",
      "blockHash": null,
      "blockNumber": null,
      "transactionIndex": null,
      "from": "0xe53e478c072265e2d9a99a4301346700c5fbb406",
      "to": "0xf5d405530dabfbd0c1cab7a5812f008aa5559adf",
      "value": "0x2efc004ac03a4996",
      "gasPrice": "0x4a817c800",
      "gas": "0x5208",
      "input": "0x",
      "creates": null,
      "raw": "0xf86c018504a817c80082520894f5d405530dabfbd0c1cab7a5812f008aa5559adf882efc004ac03a49968025a0b40c6967a7e8bbdfd99a25fd306b9ef23b80e719514aeb7ddd19e2303d6fc139a06bf770ab08119e67dc29817e1412a0e3086f43da308c314db1b3bca9fb6d32bd",
      "publicKey": "0xeba33fd74f06236e17475bc5b6d1bac718eac048350d77d3fc8fbcbd85782a57c821255623c4fd1ebc9d555d07df453b2579ee557b7203fc256ca3b3401e4027",
      "networkId": 1,
      "standardV": "0x0",
      "v": "0x25",
      "r": "0xb40c6967a7e8bbdfd99a25fd306b9ef23b80e719514aeb7ddd19e2303d6fc139",
      "s": "0x6bf770ab08119e67dc29817e1412a0e3086f43da308c314db1b3bca9fb6d32bd",
      "condition": {
        "block": 1
      }
    },
    { ... }, { ... }, ...
  ]
}
```

***

### parity_getBlockHeaderByNumber

Get block header. Same as [`eth_getBlockByNumber`](JSONRPC-eth-module.md#eth_getblockbynumber) but without uncles and transactions.

#### Parameters

0. `Quantity` | `Tag` - integer of a block number, or the string `'earliest'`, `'latest'` or `'pending'`, as in the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0x1b4" // 436
]
```

#### Returns

- `Object` - Block header

#### Example

Request
```bash
curl --data '{"method":"parity_getBlockHeaderByNumber","params":["0x1b4"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "author": "0xbb7b8287f3f0a933474a79eae42cbca977791171",
    "difficulty": "0x4ea3f27bc",
    "extraData": "0x476574682f4c5649562f76312e302e302f6c696e75782f676f312e342e32",
    "gasLimit": "0x1388",
    "gasUsed": "0x0",
    "hash": "0xdc0818cf78f21a8e70579cb46a43643f78291264dda342ae31049421c82d21ae",
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "miner": "0xbb7b8287f3f0a933474a79eae42cbca977791171",
    "mixHash": "0x4fffe9ae21f1c9e15207b1f472d5bbdd68c9595d461666602f2be20daf5e7843",
    "nonce": "0x689056015818adbe",
    "number": "0x1b4",
    "parentHash": "0xe99e022112df268087ea7eafaf4790497fd21dbeeb6bd7a1721df161a6657a54",
    "receiptsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "sealFields": [
      "0xa04fffe9ae21f1c9e15207b1f472d5bbdd68c9595d461666602f2be20daf5e7843",
      "0x88689056015818adbe"
    ],
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "size": "0x21b",
    "stateRoot": "0xddc8b0234c2e0cad087c8b389aa7ef01f7d79b2570bccb77ce48648aa61c904d",
    "timestamp": "0x55ba467c",
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"
  }
}
```

***

### parity_listOpenedVaults

Returns a list of all opened vaults

#### Parameters

None

#### Returns

- `Array` - Names of all opened vaults

#### Example

Request
```bash
curl --data '{"method":"parity_listOpenedVaults","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "['Personal']"
}
```

***

### parity_listStorageKeys

Returns all storage keys of the given address (first parameter) if Fat DB is enabled (`--fat-db`), `null` otherwise.

#### Parameters

0. `Address` - 20 Bytes - Account for which to retrieve the storage keys.
0. `Quantity` - Integer number of addresses to display in a batch.
0. `Hash` - 32 Bytes - Offset storage key from which the batch should start in order, or `null`.
0. `Quantity` | `Tag` - (optional) integer block number, or the string `'latest'`, `'earliest'` or `'pending'`.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  5,
  null
]
```

#### Returns

- `Array` - Requested number of 32 byte long storage keys for the given account or `null` if Fat DB is not enabled.

#### Example

Request
```bash
curl --data '{"method":"parity_listStorageKeys","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1",5,null],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "0xaab1a2940583e213f1d57a3ed358d5f5406177c8ff3c94516bfef3ea62d00c22",
    "0xba8469eca5641b186e86cbc5343dfa5352df04feb4564cd3cf784f213aaa0319",
    "0x769d107ba778d90205d7a159e820c41c20bf0783927b426c602561e74b7060e5",
    "0x0289865bcaa58f7f5bf875495ac7af81e3630eb88a3a0358407c7051a850624a",
    "0x32e0536502b9163b0a1ce6e3aabd95fa4a2bf602bbde1b9118015648a7a51178"
  ]
}
```

***

### parity_listVaults

Returns a list of all available vaults

#### Parameters

None

#### Returns

- `Array` - Names of all available vaults

#### Example

Request
```bash
curl --data '{"method":"parity_listVaults","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "['Personal','Work']"
}
```

***

### parity_localTransactions

Returns an object of current and past local transactions.

#### Parameters

None

#### Returns

- `Object` - Mapping of transaction hashes to status objects status object.

#### Example

Request
```bash
curl --data '{"method":"parity_localTransactions","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "0x09e64eb1ae32bb9ac415ce4ddb3dbad860af72d9377bb5f073c9628ab413c532": {
      "status": "mined",
      "transaction": {
        "from": "0x00a329c0648769a73afac7f9381e08fb43dbea72",
        "to": "0x00a289b43e1e4825dbedf2a78ba60a640634dc40",
        "value": "0xfffff",
        "blockHash": null,
        "blockNumber": null,
        "creates": null,
        "gas": "0xe57e0",
        "gasPrice": "0x2d20cff33",
        "hash": "0x09e64eb1ae32bb9ac415ce4ddb3dbad860af72d9377bb5f073c9628ab413c532",
        "input": "0x",
        "condition": {
          "block": 1
        },
        "networkId": null,
        "nonce": "0x0",
        "publicKey": "0x3fa8c08c65a83f6b4ea3e04e1cc70cbe3cd391499e3e05ab7dedf28aff9afc538200ff93e3f2b2cb5029f03c7ebee820d63a4c5a9541c83acebe293f54cacf0e",
        "raw": "0xf868808502d20cff33830e57e09400a289b43e1e4825dbedf2a78ba60a640634dc40830fffff801ca034c333b0b91cd832a3414d628e3fea29a00055cebf5ba59f7038c188404c0cf3a0524fd9b35be170439b5ffe89694ae0cfc553cb49d1d8b643239e353351531532",
        "standardV": "0x1",
        "v": "0x1c",
        "r": "0x34c333b0b91cd832a3414d628e3fea29a00055cebf5ba59f7038c188404c0cf3",
        "s": "0x524fd9b35be170439b5ffe89694ae0cfc553cb49d1d8b643239e353351531532",
        "transactionIndex": null
      }
    },
    "0x...": { ... }
  }
}
```

***

### parity_releasesInfo

returns a ReleasesInfo object describing the current status of releases

#### Parameters

None

#### Returns

- `Object` - Information on current releases, `null` if not available.
    - `fork`: `Quantity` - Block number representing the last known fork for this chain, which may be in the future.
    - `minor`: `Object` - Information about latest minor update to current version, `null` if this is the latest minor version.
    - `track`: `Object` - Information about the latest release in this track.

#### Example

Request
```bash
curl --data '{"method":"parity_releasesInfo","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": null
}
```

***

### parity_signMessage

Sign the hashed message bytes with the given account.

#### Parameters

0. `Address` - Account which signs the message.
0. `String` - Passphrase to unlock the account.
0. `Data` - Hashed message.

```js
params: [
  "0xc171033d5cbff7175f29dfd3a63dda3d6f8f385e",
  "password1",
  "0xbc36789e7a1e281436464229828f817d6612f7b477d66591ff96a9e064bcc98a"
]
```

#### Returns

- `Data` - Message signature.

#### Example

Request
```bash
curl --data '{"method":"parity_signMessage","params":["0xc171033d5cbff7175f29dfd3a63dda3d6f8f385e","password1","0xbc36789e7a1e281436464229828f817d6612f7b477d66591ff96a9e064bcc98a"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1d9e33a8cf8bfc089a172bca01da462f9e359c6cb1b0f29398bc884e4d18df4f78588aee4fb5cc067ca62d2abab995e0bba29527be6ac98105b0320020a2efaf00"
}
```

***

### parity_versionInfo

Provides information about running version of Parity.

#### Parameters

None

#### Returns

- `Object` - Information on current version.
    - `hash`: `Hash` - 20 Byte hash of the current build.
    - `track`: `String` - Track on which it was released, one of: `"stable"`, `"beta"`, `"nightly"`, `"testing"`, `"null"` (unknown or self-built).
    - `version`: `Object` - Version number composed of `major`, `minor` and `patch` integers.

#### Example

Request
```bash
curl --data '{"method":"parity_versionInfo","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "hash": "0x2ae8b4ca278dd7b896090366615fef81cbbbc0e0",
    "track": "null",
    "version": {
      "major": 1,
      "minor": 6,
      "patch": 0
    }
  }
}
```

***

### parity_changeVault

Changes the current vault for the account

#### Parameters

0. `Address` - Account address
0. `String` - Vault name

```js
params: [
  "0x63Cf90D3f0410092FC0fca41846f596223979195",
  "StrongVault"
]
```

#### Returns

- `Boolean` - True on success

#### Example

Request
```bash
curl --data '{"method":"parity_changeVault","params":["0x63Cf90D3f0410092FC0fca41846f596223979195","StrongVault"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_changeVaultPassword

Changes the password for any given vault

#### Parameters

0. `String` - Vault name
0. `String` - New Password

```js
params: [
  "StrongVault",
  "p@55w0rd"
]
```

#### Returns

- `Boolean` - True on success

#### Example

Request
```bash
curl --data '{"method":"parity_changeVaultPassword","params":["StrongVault","p@55w0rd"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_closeVault

Closes a vault with the given name

#### Parameters

0. `String` - Vault name

```js
params: ["StrongVault"]
```

#### Returns

- `Boolean` - True on success

#### Example

Request
```bash
curl --data '{"method":"parity_closeVault","params":["StrongVault"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_getVaultMeta

Returns the metadata for a specific vault

#### Parameters

0. `String` - Vault name

```js
params: ["StrongVault"]
```

#### Returns

- `String` - The associated JSON metadata for this vault

#### Example

Request
```bash
curl --data '{"method":"parity_getVaultMeta","params":["StrongVault"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "{\"passwordHint\":\"something\"}"
}
```

***

### parity_newVault

Creates a new vault with the given name & password

#### Parameters

0. `String` - Vault name
0. `String` - Password

```js
params: [
  "StrongVault",
  "p@55w0rd"
]
```

#### Returns

- `Boolean` - True on success

#### Example

Request
```bash
curl --data '{"method":"parity_newVault","params":["StrongVault","p@55w0rd"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_openVault

Opens a vault with the given name & password

#### Parameters

0. `String` - Vault name
0. `String` - Password

```js
params: [
  "StrongVault",
  "p@55w0rd"
]
```

#### Returns

- `Boolean` - True on success

#### Example

Request
```bash
curl --data '{"method":"parity_openVault","params":["StrongVault","p@55w0rd"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setVaultMeta

Sets the metadata for a specific vault

#### Parameters

0. `String` - Vault name
0. `String` - The metadata as a JSON string

```js
params: [
  "StrongVault",
  "{\"passwordHint\":\"something\"}"
]
```

#### Returns

- `Boolean` - The boolean call result, true on success

#### Example

Request
```bash
curl --data '{"method":"parity_setVaultMeta","params":["StrongVault","{\"passwordHint\":\"something\"}"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_accountsInfo

Provides metadata for accounts.

#### Parameters

None

#### Returns

- `Object` - Maps account address to metadata.
    - `name`: `String` - Account name

#### Example

Request
```bash
curl --data '{"method":"parity_accountsInfo","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "0x0024d0c7ab4c52f723f3aaf0872b9ea4406846a4": {
      "name": "Foo"
    },
    "0x004385d8be6140e6f889833f68b51e17b6eacb29": {
      "name": "Bar"
    },
    "0x009047ed78fa2be48b62aaf095b64094c934dab0": {
      "name": "Baz"
    }
  }
}
```

***

### parity_checkRequest

Get the the transaction hash of the request previously posted to [`parity_postTransaction`](#parity_posttransaction) or [`parity_postSign`](#parity_postsign). Will return a JSON-RPC error if the request was rejected.

#### Parameters

0. `Quantity` - The id of the request sent to the signer.

```js
params: ["0x1"]
```

#### Returns

- `Hash` - 32 Bytes - the transaction hash or `null` if the request hasn't been signed yet.

#### Example

Request
```bash
curl --data '{"method":"parity_checkRequest","params":["0x1"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xde8dfd9642f7eeef12402f2a560dbf40921b4f0bda01fb84709b9d71f6c181be"
}
```

***

### parity_defaultAccount

Returns the defaultAccount that is to be used with transactions

#### Parameters

None

#### Returns

- `Address` - The account address

#### Example

Request
```bash
curl --data '{"method":"parity_defaultAccount","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x63Cf90D3f0410092FC0fca41846f596223979195"
}
```

***

### parity_generateSecretPhrase

Creates a secret phrase that can be associated with an account.

#### Parameters

None

#### Returns

- `String` - The secret phrase.

#### Example

Request
```bash
curl --data '{"method":"parity_generateSecretPhrase","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "boasting breeches reshape reputably exit handrail stony jargon moneywise unhinge handed ruby"
}
```

***

### parity_hardwareAccountsInfo

Provides metadata for attached hardware wallets

#### Parameters

None

#### Returns

- `Object` - Maps account address to metadata.
    - `manufacturer`: `String` - Manufacturer
    - `name`: `String` - Account name

#### Example

Request
```bash
curl --data '{"method":"parity_hardwareAccountsInfo","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "0x0024d0c7ab4c52f723f3aaf0872b9ea4406846a4": {
      "manufacturer": "Ledger",
      "name": "Nano S"
    }
  }
}
```

***

### parity_listAccounts

Returns all addresses if Fat DB is enabled (`--fat-db`), `null` otherwise.

#### Parameters

0. `Quantity` - Integer number of addresses to display in a batch.
0. `Address` - 20 Bytes - Offset address from which the batch should start in order, or `null`.
0. `Quantity` | `Tag` - (optional) integer block number, or the string `'latest'`, `'earliest'` or `'pending'`.

```js
params: [
  5,
  null
]
```

#### Returns

- `Array` - Requested number of `Address`es or `null` if Fat DB is not enabled.

#### Example

Request
```bash
curl --data '{"method":"parity_listAccounts","params":[5,null],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "0x7205b1bb42edce6e0ced37d1fd0a9d684f5a860f",
    "0x98a2559a814c300b274325c92df1682ae0d344e3",
    "0x2d7a7d0adf9c5f9073fefbdc18188bd23c68b633",
    "0xd4bb3284201db8b03c06d8a3057dd32538e3dfda",
    "0xa6396904b08aa31300ca54278b8e066ecc38e4a0"
  ]
}
```

***

### parity_phraseToAddress

Converts a secret phrase into the corresponding address.

#### Parameters

0. `String` - The phrase

```js
params: ["stylus outing overhand dime radial seducing harmless uselessly evasive tastiness eradicate imperfect"]
```

#### Returns

- `Address` - Corresponding address

#### Example

Request
```bash
curl --data '{"method":"parity_phraseToAddress","params":["stylus outing overhand dime radial seducing harmless uselessly evasive tastiness eradicate imperfect"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x004385d8be6140e6f889833f68b51e17b6eacb29"
}
```

***

### parity_postSign

Request a standard Ethereum message to be signed by an account.

#### Parameters

0. `Address` - Account address.
0. `Data` - The message.

```js
params: [
  "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "0x414243"
]
```

#### Returns

- `Quantity` - The id of the request to the signer.

#### Example

Request
```bash
curl --data '{"method":"parity_postSign","params":["0xb60e8dd61c5d32be8058bb8eb970870f07233155","0x8cda01991ae267a539135736132f1f987e76868ce0269b7537d3aab37b7b185e"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1"
}
```

***

### parity_postTransaction

Posts a transaction to the signer without waiting for the signer response.

#### Parameters

0. `Object` - see [`eth_sendTransaction`](JSONRPC-eth-module.md#eth_sendtransaction).
    - `from`: `Address` - 20 Bytes - The address the transaction is send from.
    - `to`: `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`: `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`: `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`: `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`: `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`: `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`: `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

```js
params: [{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
  "gas": "0x76c0", // 30400
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
  "condition": {
    "block": 354221,
    "time": "2017-07-31T08:53:46.544Z"
  }
}]
```

#### Returns

- `Quantity` - The id of the request to the signer. If the account was already unlocked, returns `Hash` of the transaction instead.

#### Example

Request
```bash
curl --data '{"method":"parity_postTransaction","params":[{"from":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","to":"0xd46e8dd67c5d32be8058bb8eb970870f07244567","gas":"0x76c0","gasPrice":"0x9184e72a000","value":"0x9184e72a","data":"0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675","condition":{"block":354221,"time":"2017-07-31T08:53:46.544Z"}}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1"
}
```

***

### parity_defaultExtraData

Returns the default extra data

#### Parameters

None

#### Returns

- `Data` - Extra data

#### Example

Request
```bash
curl --data '{"method":"parity_defaultExtraData","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xd5830106008650617269747986312e31342e30826c69"
}
```

***

### parity_extraData

Returns currently set extra data.

#### Parameters

None

#### Returns

- `Data` - Extra data.

#### Example

Request
```bash
curl --data '{"method":"parity_extraData","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xd5830106008650617269747986312e31342e30826c69"
}
```

***

### parity_gasCeilTarget

Returns current target for gas ceiling.

#### Parameters

None

#### Returns

- `Quantity` - Gas ceiling target.

#### Example

Request
```bash
curl --data '{"method":"parity_gasCeilTarget","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x5fdfb0" // 6283184
}
```

***

### parity_gasFloorTarget

Returns current target for gas floor.

#### Parameters

None

#### Returns

- `Quantity` - Gas floor target.

#### Example

Request
```bash
curl --data '{"method":"parity_gasFloorTarget","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x47b760" // 4700000
}
```

***

### parity_minGasPrice

Returns currently set minimal gas price

#### Parameters

None

#### Returns

- `Quantity` - Minimal Gas Price

#### Example

Request
```bash
curl --data '{"method":"parity_minGasPrice","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x29f507000" // 11262783488
}
```

***

### parity_transactionsLimit

Changes limit for transactions in queue.

#### Parameters

None

#### Returns

- `Quantity` - Current max number of transactions in queue.

#### Example

Request
```bash
curl --data '{"method":"parity_transactionsLimit","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": 1024
}
```

***

### parity_devLogs

Returns latest stdout logs of your node.

#### Parameters

None

#### Returns

- `Array` - Development logs

#### Example

Request
```bash
curl --data '{"method":"parity_devLogs","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "2017-01-20 18:14:19  Updated conversion rate to Ξ1 = US$10.63 (11199212000 wei/gas)",
    "2017-01-20 18:14:19  Configured for DevelopmentChain using InstantSeal engine",
    "2017-01-20 18:14:19  Operating mode: active",
    "2017-01-20 18:14:19  State DB configuration: fast",
    "2017-01-20 18:14:19  Starting Parity/v1.7.0-unstable-2ae8b4c-20170120/x86_64-linux-gnu/rustc1.14.0"
  ]
}
```

***

### parity_devLogsLevels

Returns current logging level settings. Logging level can be set with `--logging` and be one of: `""` (default), `"info"`, `"debug"`, `"warn"`, `"error"`, `"trace"`.

#### Parameters

None

#### Returns

- `String` - undefined

#### Example

Request
```bash
curl --data '{"method":"parity_devLogsLevels","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "debug"
}
```

***

### parity_chain

Returns the name of the connected chain.

#### Parameters

None

#### Returns

- `String` - chain name, one of: "foundation", "kovan", &c. of a filename.

#### Example

Request
```bash
curl --data '{"method":"parity_chain","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "homestead"
}
```

***

### parity_chainId

Returns the EIP155 chain ID used for transaction signing at the current best block. Null is returned if not available.

#### Parameters

None

#### Returns

- `Quantity` - EIP155 Chain ID, or `null` if not available.

#### Example

Request
```bash
curl --data '{"method":"parity_chainId","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1"
}
```

***

### parity_chainStatus

Returns the information on warp sync blocks

#### Parameters

None

#### Returns

- `Object` - The status object
    - `blockGap`: `Array` - (optional) Describes the gap in the blockchain, if there is one: (first, last)

#### Example

Request
```bash
curl --data '{"method":"parity_chainStatus","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "blockGap": undefined
  }
}
```

***

### parity_gasPriceHistogram

Returns a snapshot of the historic gas prices.

#### Parameters

None

#### Returns

- `Object` - Historic values
    - `bucketBounds`: `Array` - Array of bound values.
    - `count`: `Array` - Array of counts.

#### Example

Request
```bash
curl --data '{"method":"parity_gasPriceHistogram","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "bucketBounds": [
      "0x4a817c800",
      "0x525433d01",
      "0x5a26eb202",
      "0x61f9a2703",
      "0x69cc59c04",
      "0x719f11105",
      "0x7971c8606",
      "0x81447fb07",
      "0x891737008",
      "0x90e9ee509",
      "0x98bca5a0a"
    ],
    "counts": [
      487,
      9,
      7,
      1,
      8,
      0,
      0,
      0,
      0,
      14
    ]
  }
}
```

***

### parity_netChain

Returns the name of the connected chain. DEPRECATED use `parity_chain` instead.

#### Parameters

None

#### Returns

- `String` - chain name.

#### Example

Request
```bash
curl --data '{"method":"parity_netChain","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "homestead"
}
```

***

### parity_netPeers

Returns number of peers.

#### Parameters

None

#### Returns

- `Object` - Number of peers
    - `active`: `Quantity` - Number of active peers.
    - `connected`: `Quantity` - Number of connected peers.
    - `max`: `Quantity` - Maximum number of connected peers.
    - `peers`: `Array` - List of all peers with details.

#### Example

Request
```bash
curl --data '{"method":"parity_netPeers","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "active": 0,
    "connected": 25,
    "max": 25,
    "peers": [{ ... }, { ... }, { ... }, ...]
  }
}
```

***

### parity_netPort

Returns network port the node is listening on.

#### Parameters

None

#### Returns

- `Quantity` - Port number

#### Example

Request
```bash
curl --data '{"method":"parity_netPort","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": 30303
}
```

***

### parity_nextNonce

Returns next available nonce for transaction from given account. Includes pending block and transaction queue.

#### Parameters

0. `Address` - Account

```js
params: ["0x00A289B43e1e4825DbEDF2a78ba60a640634DC40"]
```

#### Returns

- `Quantity` - Next valid nonce

#### Example

Request
```bash
curl --data '{"method":"parity_nextNonce","params":["0x00A289B43e1e4825DbEDF2a78ba60a640634DC40"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xc" // 12
}
```

***

### parity_pendingTransactions

Returns a list of transactions currently in the queue.

#### Parameters

None

#### Returns

- `Array` - Transactions ordered by priority
    - `hash`: `Hash` - 32 Bytes - hash of the transaction.
    - `nonce`: `Quantity` - The number of transactions made by the sender prior to this one.
    - `blockHash`: `Hash` - 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
    - `blockNumber`: `Quantity` | `Tag` - Block number where this transaction was in. `null` when its pending.
    - `transactionIndex`: `Quantity` - Integer of the transactions index position in the block. `null` when its pending.
    - `from`: `Address` - 20 Bytes - address of the sender.
    - `to`: `Address` - 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
    - `value`: `Quantity` - Value transferred in Wei.
    - `gasPrice`: `Quantity` - Gas price provided by the sender in Wei.
    - `gas`: `Quantity` - Gas provided by the sender.
    - `input`: `Data` - The data send along with the transaction.
    - `creates`: `Address` - (optional) Address of a created contract or `null`.
    - `raw`: `Data` - Raw transaction data.
    - `publicKey`: `Data` - Public key of the signer.
    - `networkId`: `Quantity` - The network id of the transaction, if any.
    - `standardV`: `Quantity` - The standardized V field of the signature (0 or 1).
    - `v`: `Quantity` - The V field of the signature.
    - `r`: `Quantity` - The R field of the signature.
    - `s`: `Quantity` - The S field of the signature.
    - `condition`: `Object` - (optional) Conditional submission, Block number in `block` or timestamp in `time` or `null`.

#### Example

Request
```bash
curl --data '{"method":"parity_pendingTransactions","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    {
      "blockHash": null,
      "blockNumber": null,
      "creates": null,
      "from": "0xee3ea02840129123d5397f91be0391283a25bc7d",
      "gas": "0x23b58",
      "gasPrice": "0xba43b7400",
      "hash": "0x160b3c30ab1cf5871083f97ee1cee3901cfba3b0a2258eb337dd20a7e816b36e",
      "input": "0x095ea7b3000000000000000000000000bf4ed7b27f1d666546e30d74d50d173d20bca75400000000000000000000000000002643c948210b4bd99244ccd64d5555555555",
      "condition": {
        "block": 1
      },
      "networkId": 1,
      "nonce": "0x5",
      "publicKey": "0x96157302dade55a1178581333e57d60ffe6fdf5a99607890456a578b4e6b60e335037d61ed58aa4180f9fd747dc50d44a7924aa026acbfb988b5062b629d6c36",
      "r": "0x92e8beb19af2bad0511d516a86e77fa73004c0811b2173657a55797bdf8558e1",
      "raw": "0xf8aa05850ba43b740083023b5894bb9bc244d798123fde783fcc1c72d3bb8c18941380b844095ea7b3000000000000000000000000bf4ed7b27f1d666546e30d74d50d173d20bca75400000000000000000000000000002643c948210b4bd99244ccd64d555555555526a092e8beb19af2bad0511d516a86e77fa73004c0811b2173657a55797bdf8558e1a062b4d4d125bbcb9c162453bc36ca156537543bb4414d59d1805d37fb63b351b8",
      "s": "0x62b4d4d125bbcb9c162453bc36ca156537543bb4414d59d1805d37fb63b351b8",
      "standardV": "0x1",
      "to": "0xbb9bc244d798123fde783fcc1c72d3bb8c189413",
      "transactionIndex": null,
      "v": "0x26",
      "value": "0x0"
    },
    { ... },
    { ... }
  ]
}
```

***

### parity_pendingTransactionsStats

Returns propagation stats for transactions in the queue.

#### Parameters

None

#### Returns

- `Object` - mapping of transaction hashes to stats.

#### Example

Request
```bash
curl --data '{"method":"parity_pendingTransactionsStats","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "0xdff37270050bcfba242116c745885ce2656094b2d3a0f855649b4a0ee9b5d15a": {
      "firstSeen": 3032066,
      "propagatedTo": {
        "0x605e04a43b1156966b3a3b66b980c87b7f18522f7f712035f84576016be909a2798a438b2b17b1a8c58db314d88539a77419ca4be36148c086900fba487c9d39": 1,
        "0xbab827781c852ecf52e7c8bf89b806756329f8cbf8d3d011e744a0bc5e3a0b0e1095257af854f3a8415ebe71af11b0c537f8ba797b25972f519e75339d6d1864": 1
      }
    }
  }
}
```

***

### parity_registryAddress

The address for the global registry.

#### Parameters

None

#### Returns

- `Address` - The registry address.

#### Example

Request
```bash
curl --data '{"method":"parity_registryAddress","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x3bb2bb5c6c9c9b7f4ef430b47dc7e026310042ea"
}
```

***

### parity_removeTransaction

Removes transaction from local transaction pool. Scheduled transactions and not-propagated transactions are safe to remove, removal of other transactions may have no effect though.

#### Parameters

0. `Hash` - Hash of transaction to remove.

```js
params: ["0x2547ea3382099c7c76d33dd468063b32d41016aacb02cbd51ebc14ff5d2b6a43"]
```

#### Returns

- `Object` - Removed transaction or `null`.
    - `hash`: `Hash` - 32 Bytes - hash of the transaction.
    - `nonce`: `Quantity` - The number of transactions made by the sender prior to this one.
    - `blockHash`: `Hash` - 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
    - `blockNumber`: `Quantity` | `Tag` - Block number where this transaction was in. `null` when its pending.
    - `transactionIndex`: `Quantity` - Integer of the transactions index position in the block. `null` when its pending.
    - `from`: `Address` - 20 Bytes - address of the sender.
    - `to`: `Address` - 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
    - `value`: `Quantity` - Value transferred in Wei.
    - `gasPrice`: `Quantity` - Gas price provided by the sender in Wei.
    - `gas`: `Quantity` - Gas provided by the sender.
    - `input`: `Data` - The data send along with the transaction.
    - `creates`: `Address` - (optional) Address of a created contract or `null`.
    - `raw`: `Data` - Raw transaction data.
    - `publicKey`: `Data` - Public key of the signer.
    - `networkId`: `Quantity` - The network id of the transaction, if any.
    - `standardV`: `Quantity` - The standardized V field of the signature (0 or 1).
    - `v`: `Quantity` - The V field of the signature.
    - `r`: `Quantity` - The R field of the signature.
    - `s`: `Quantity` - The S field of the signature.
    - `condition`: `Object` - (optional) Conditional submission, Block number in `block` or timestamp in `time` or `null`.

#### Example

Request
```bash
curl --data '{"method":"parity_removeTransaction","params":["0x2547ea3382099c7c76d33dd468063b32d41016aacb02cbd51ebc14ff5d2b6a43"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    {
      "blockHash": null,
      "blockNumber": null,
      "creates": null,
      "from": "0xee3ea02840129123d5397f91be0391283a25bc7d",
      "gas": "0x23b58",
      "gasPrice": "0xba43b7400",
      "hash": "0x160b3c30ab1cf5871083f97ee1cee3901cfba3b0a2258eb337dd20a7e816b36e",
      "input": "0x095ea7b3000000000000000000000000bf4ed7b27f1d666546e30d74d50d173d20bca75400000000000000000000000000002643c948210b4bd99244ccd64d5555555555",
      "condition": {
        "block": 1
      },
      "networkId": 1,
      "nonce": "0x5",
      "publicKey": "0x96157302dade55a1178581333e57d60ffe6fdf5a99607890456a578b4e6b60e335037d61ed58aa4180f9fd747dc50d44a7924aa026acbfb988b5062b629d6c36",
      "r": "0x92e8beb19af2bad0511d516a86e77fa73004c0811b2173657a55797bdf8558e1",
      "raw": "0xf8aa05850ba43b740083023b5894bb9bc244d798123fde783fcc1c72d3bb8c18941380b844095ea7b3000000000000000000000000bf4ed7b27f1d666546e30d74d50d173d20bca75400000000000000000000000000002643c948210b4bd99244ccd64d555555555526a092e8beb19af2bad0511d516a86e77fa73004c0811b2173657a55797bdf8558e1a062b4d4d125bbcb9c162453bc36ca156537543bb4414d59d1805d37fb63b351b8",
      "s": "0x62b4d4d125bbcb9c162453bc36ca156537543bb4414d59d1805d37fb63b351b8",
      "standardV": "0x1",
      "to": "0xbb9bc244d798123fde783fcc1c72d3bb8c189413",
      "transactionIndex": null,
      "v": "0x26",
      "value": "0x0"
    },
    { ... },
    { ... }
  ]
}
```

***

### parity_rpcSettings

Provides current JSON-RPC API settings.

#### Parameters

None

#### Returns

- `Object` - JSON-RPC settings.
    - `enabled`: `Boolean` - `true` if JSON-RPC is enabled (default).
    - `interface`: `String` - Interface on which JSON-RPC is running.
    - `port`: `Quantity` - Port on which JSON-RPC is running.

#### Example

Request
```bash
curl --data '{"method":"parity_rpcSettings","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "enabled": true,
    "interface": "local",
    "port": 8545
  }
}
```

***

### parity_unsignedTransactionsCount

Returns number of unsigned transactions when running with Trusted Signer. Error otherwise

#### Parameters

None

#### Returns

- `Quantity` - Number of unsigned transactions

#### Example

Request
```bash
curl --data '{"method":"parity_unsignedTransactionsCount","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": 0
}
```

***

### parity_dappsUrl

Returns the hostname and the port of dapps/rpc server, error if not enabled.

#### Parameters

None

#### Returns

- `String` - The hostname and port number

#### Example

Request
```bash
curl --data '{"method":"parity_dappsUrl","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "localhost:8545"
}
```

***

### parity_enode

Returns the node enode URI.

#### Parameters

None

#### Returns

- `String` - Enode URI

#### Example

Request
```bash
curl --data '{"method":"parity_enode","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "enode://050929adcfe47dbe0b002cb7ef2bf91ca74f77c4e0f68730e39e717f1ce38908542369ae017148bee4e0d968340885e2ad5adea4acd19c95055080a4b625df6a@172.17.0.1:30303"
}
```

***

### parity_mode

Get the mode. Results one of: `"active"`, `"passive"`, `"dark"`, `"offline"`.

#### Parameters

None

#### Returns

- `String` - The mode.

#### Example

Request
```bash
curl --data '{"method":"parity_mode","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "active"
}
```

***

### parity_nodeKind

Returns the node type availability and capability

#### Parameters

None

#### Returns

- `Object` - Availability and Capability.
    - `availability`: `String` - Availability, either `personal` or `public`.
    - `capability`: `String` - Capability, either `full` or `light`.

#### Example

Request
```bash
curl --data '{"method":"parity_nodeKind","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "availability": "personal",
    "capability": "light"
  }
}
```

***

### parity_nodeName

Returns node name, set when starting parity with `--identity NAME`.

#### Parameters

None

#### Returns

- `String` - Node name.

#### Example

Request
```bash
curl --data '{"method":"parity_nodeName","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "Doge"
}
```

***

### parity_wsUrl

Returns the hostname and the port of WebSockets/Signer server, error if not enabled.

#### Parameters

None

#### Returns

- `String` - The hostname and port number

#### Example

Request
```bash
curl --data '{"method":"parity_wsUrl","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "localhost:8546"
}
```
