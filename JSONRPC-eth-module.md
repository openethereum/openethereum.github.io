---
title: The `eth` Module
---

## The default block parameter

The following methods have an optional extra `defaultBlock` parameter:

- [eth_estimateGas](#eth_estimategas)
- [eth_getBalance](#eth_getbalance)
- [eth_getCode](#eth_getcode)
- [eth_getTransactionCount](#eth_gettransactioncount)
- [eth_getStorageAt](#eth_getstorageat)
- [eth_call](#eth_call)

When requests are made that act on the state of Ethereum, the last parameter determines the height of the block.

The following options are possible for the `defaultBlock` parameter:

- `Quantity`/`Integer` - an integer block number;
- `String "earliest"` - for the earliest/genesis block;
- `String "latest"` - for the latest mined block;
- `String "pending"` - for the pending state/transactions.

## JSON-RPC methods

- [eth_accounts](#eth_accounts)
- [eth_blockNumber](#eth_blocknumber)
- [eth_call](#eth_call)
- [eth_chainId](#eth_chainid)
- [eth_coinbase](#eth_coinbase)
- [eth_estimateGas](#eth_estimategas)
- [eth_gasPrice](#eth_gasprice)
- [eth_getBalance](#eth_getbalance)
- [eth_getBlockByHash](#eth_getblockbyhash)
- [eth_getBlockByNumber](#eth_getblockbynumber)
- [eth_getBlockTransactionCountByHash](#eth_getblocktransactioncountbyhash)
- [eth_getBlockTransactionCountByNumber](#eth_getblocktransactioncountbynumber)
- [eth_getCode](#eth_getcode)
- [eth_getFilterChanges](#eth_getfilterchanges)
- [eth_getFilterLogs](#eth_getfilterlogs)
- [eth_getLogs](#eth_getlogs)
- [eth_getStorageAt](#eth_getstorageat)
- [eth_getTransactionByBlockHashAndIndex](#eth_gettransactionbyblockhashandindex)
- [eth_getTransactionByBlockNumberAndIndex](#eth_gettransactionbyblocknumberandindex)
- [eth_getTransactionByHash](#eth_gettransactionbyhash)
- [eth_getTransactionCount](#eth_gettransactioncount)
- [eth_getTransactionReceipt](#eth_gettransactionreceipt)
- [eth_getUncleByBlockHashAndIndex](#eth_getunclebyblockhashandindex)
- [eth_getUncleByBlockNumberAndIndex](#eth_getunclebyblocknumberandindex)
- [eth_getUncleCountByBlockHash](#eth_getunclecountbyblockhash)
- [eth_getUncleCountByBlockNumber](#eth_getunclecountbyblocknumber)
- [eth_getWork](#eth_getwork)
- [eth_hashrate](#eth_hashrate)
- [eth_mining](#eth_mining)
- [eth_newBlockFilter](#eth_newblockfilter)
- [eth_newFilter](#eth_newfilter)
- [eth_newPendingTransactionFilter](#eth_newpendingtransactionfilter)
- [eth_protocolVersion](#eth_protocolversion)
- [eth_sendRawTransaction](#eth_sendrawtransaction)
- [eth_sendTransaction](#eth_sendtransaction)
- [eth_sign](#eth_sign)
- [eth_signTransaction](#eth_signtransaction)
- [eth_submitHashrate](#eth_submithashrate)
- [eth_submitWork](#eth_submitwork)
- [eth_syncing](#eth_syncing)
- [eth_uninstallFilter](#eth_uninstallfilter)

## JSON-RPC API Reference

### eth_accounts

Returns a list of addresses owned by client.

#### Parameters

None

#### Returns

- `Array` - 20 Bytes - addresses owned by the client.

#### Example

Request
```bash
curl --data '{"method":"eth_accounts","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
}
```

***

### eth_blockNumber

Returns the number of most recent block.

#### Parameters

None

#### Returns

- `Quantity` - integer of the current block number the client is on.

#### Example

Request
```bash
curl --data '{"method":"eth_blockNumber","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x4b7" // 1207
}
```

***

### eth_call

Executes a new message call immediately without creating a transaction on the block chain.

#### Parameters

0. `Object` - The transaction call object.
    - `from`:   `Address` - (optional) 20 Bytes - The address the transaction is send from.
    - `to`:   `Address` - (optional when creating new contract) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
0. `Quantity` or `Tag` - (optional) Integer block number, or the string `'latest'`, `'earliest'` or `'pending'`, see the [default block parameter](#the-default-block-parameter).

```js
params: [{
  "from": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "to": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
  "value": "0x186a0" // 100000
}]
```

#### Returns

- `Data` - the return value of executed contract.

#### Example

Request
```bash
curl --data '{"method":"eth_call","params":[{"from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1","to":"0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b","value":"0x186a0"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x"
}
```

***

### eth_chainId

Returns the EIP155 chain ID used for transaction signing at the current best block. Null is returned if not available.

#### Parameters

None

#### Returns

- `Quantity` - EIP155 Chain ID, or `null` if not available.

#### Example

Request
```bash
curl --data '{"method":"eth_chainId","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### eth_coinbase

Returns the client coinbase address.

#### Parameters

None

#### Returns

- `Address` - The current coinbase address.

#### Example

Request
```bash
curl --data '{"method":"eth_coinbase","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x407d73d8a49eeb85d32cf465507dd71d507100c1"
}
```

***

### eth_estimateGas

Makes a call or transaction, which won't be added to the blockchain and returns the used gas, which can be used for estimating the used gas.

#### Parameters

0. `Object` - Same as [eth_call](#eth_call) parameters, except that all properties are optional.
    - `from`:   `Address` - (optional) 20 Bytes - The address the transaction is send from.
    - `to`:   `Address` - (optional when creating new contract) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
0. `Quantity` or `Tag` - (optional) Integer block number, or the string `'latest'`, `'earliest'` or `'pending'`, see the [default block parameter](#the-default-block-parameter).

#### Returns

- `Quantity` - The amount of gas used.

#### Example

Request
```bash
curl --data '{"method":"eth_estimateGas","params":[{ ... }],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x5208" // 21000
}
```

***

### eth_gasPrice

Returns the current price per gas in wei.

#### Parameters

None

#### Returns

- `Quantity` - integer of the current gas price in wei.

#### Example

Request
```bash
curl --data '{"method":"eth_gasPrice","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x9184e72a000" // 10000000000000
}
```

***

### eth_getBalance

Returns the balance of the account of given address.

#### Parameters

0. `Address` - 20 Bytes - address to check for balance.
0. `Quantity` or `Tag` - (optional) integer block number, or the string `'latest'`, `'earliest'` or `'pending'`, see the [default block parameter](#the-default-block-parameter).

```js
params: ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
```

#### Returns

- `Quantity` - integer of the current balance in wei.

#### Example

Request
```bash
curl --data '{"method":"eth_getBalance","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x0234c8a3397aab58"
}
```

***

### eth_getBlockByHash

Returns information about a block by hash.

#### Parameters

0. `Hash` - Hash of a block.
0. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
  "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
  true
]
```

#### Returns

- `Object` - A block object, or `null` when no block was found.
    - `number`:   `Quantity` - The block number. `null` when its pending block
    - `hash`:   `Hash` - 32 Bytes - hash of the block. `null` when its pending block
    - `parentHash`:   `Hash` - 32 Bytes - hash of the parent block
    - `nonce`:   `Data` - 8 Bytes - hash of the generated proof-of-work. `null` when its pending block. Missing in case of PoA.
    - `sha3Uncles`:   `Data` - 32 Bytes - SHA3 of the uncles data in the block
    - `logsBloom`:   `Data` - 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block
    - `transactionsRoot`:   `Data` - 32 Bytes - the root of the transaction trie of the block
    - `stateRoot`:   `Data` - 32 Bytes - the root of the final state trie of the block
    - `receiptsRoot`:   `Data` - 32 Bytes - the root of the receipts trie of the block
    - `author`:   `Address` - 20 Bytes - the address of the author of the block (the beneficiary to whom the mining rewards were given)
    - `miner`:   `Address` - 20 Bytes - alias of 'author'
    - `difficulty`:   `Quantity` - integer of the difficulty for this block
    - `totalDifficulty`:   `Quantity` - integer of the total difficulty of the chain until this block
    - `extraData`:   `Data` - the 'extra data' field of this block
    - `size`:   `Quantity` - integer the size of this block in bytes
    - `gasLimit`:   `Quantity` - the maximum gas allowed in this block
    - `gasUsed`:   `Quantity` - the total used gas by all transactions in this block
    - `timestamp`:   `Quantity` - the unix timestamp for when the block was collated
    - `transactions`:   `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter
    - `uncles`:   `Array` - Array of uncle hashes

#### Example

Request
```bash
curl --data '{"method":"eth_getBlockByHash","params":["0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",true],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "number": "0x1b4", // 436
    "hash": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "parentHash": "0x9646252be9520f6e71339a8df9c55e4d7619deeb018d2a3f2d21fc165dde5eb5",
    "sealFields": [
      "0xe04d296d2460cfb8472af2c5fd05b5a214109c25688d3704aed5484f9a7792f2",
      "0x0000000000000042"
    ],
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "logsBloom": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "stateRoot": "0xd5855eb08b3387c0af375e9cdb6acfc05eb8f519e419b874b6ff2ffda7ed1dff",
    "miner": "0x4e65fda2159562a496f9f3522f89122a3088497a",
    "difficulty": "0x27f07", // 163591
    "totalDifficulty": "0x27f07", // 163591
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "size": "0x27f07", // 163591
    "gasLimit": "0x9f759", // 653145
    "minGasPrice": "0x9f759", // 653145
    "gasUsed": "0x9f759", // 653145
    "timestamp": "0x54e34e8e", // 1424182926
    "transactions": [{ ... }, { ... }, ...],
    "uncles": [
      "0x1606e5...",
      "0xd5145a9..."
    ]
  }
}
```

***

### eth_getBlockByNumber

Returns information about a block by block number.

#### Parameters

0. `Quantity` or `Tag` - integer of a block number, or the string `'earliest'`, `'latest'` or `'pending'`, as in the [default block parameter](#the-default-block-parameter).
0. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
  "0x1b4", // 436
  true
]
```

#### Returns

- `Object` - A block object, or `null` when no block was found.
    - `number`:   `Quantity` - The block number. `null` when its pending block
    - `hash`:   `Hash` - 32 Bytes - hash of the block. `null` when its pending block
    - `parentHash`:   `Hash` - 32 Bytes - hash of the parent block
    - `nonce`:   `Data` - 8 Bytes - hash of the generated proof-of-work. `null` when its pending block. Missing in case of PoA.
    - `sha3Uncles`:   `Data` - 32 Bytes - SHA3 of the uncles data in the block
    - `logsBloom`:   `Data` - 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block
    - `transactionsRoot`:   `Data` - 32 Bytes - the root of the transaction trie of the block
    - `stateRoot`:   `Data` - 32 Bytes - the root of the final state trie of the block
    - `receiptsRoot`:   `Data` - 32 Bytes - the root of the receipts trie of the block
    - `author`:   `Address` - 20 Bytes - the address of the author of the block (the beneficiary to whom the mining rewards were given)
    - `miner`:   `Address` - 20 Bytes - alias of 'author'
    - `difficulty`:   `Quantity` - integer of the difficulty for this block
    - `totalDifficulty`:   `Quantity` - integer of the total difficulty of the chain until this block
    - `extraData`:   `Data` - the 'extra data' field of this block
    - `size`:   `Quantity` - integer the size of this block in bytes
    - `gasLimit`:   `Quantity` - the maximum gas allowed in this block
    - `gasUsed`:   `Quantity` - the total used gas by all transactions in this block
    - `timestamp`:   `Quantity` - the unix timestamp for when the block was collated
    - `transactions`:   `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter
    - `uncles`:   `Array` - Array of uncle hashes

#### Example

Request
```bash
curl --data '{"method":"eth_getBlockByNumber","params":["0x1b4",true],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "number": "0x1b4", // 436
    "hash": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "parentHash": "0x9646252be9520f6e71339a8df9c55e4d7619deeb018d2a3f2d21fc165dde5eb5",
    "sealFields": [
      "0xe04d296d2460cfb8472af2c5fd05b5a214109c25688d3704aed5484f9a7792f2",
      "0x0000000000000042"
    ],
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "logsBloom": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "stateRoot": "0xd5855eb08b3387c0af375e9cdb6acfc05eb8f519e419b874b6ff2ffda7ed1dff",
    "miner": "0x4e65fda2159562a496f9f3522f89122a3088497a",
    "difficulty": "0x27f07", // 163591
    "totalDifficulty": "0x27f07", // 163591
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "size": "0x27f07", // 163591
    "gasLimit": "0x9f759", // 653145
    "minGasPrice": "0x9f759", // 653145
    "gasUsed": "0x9f759", // 653145
    "timestamp": "0x54e34e8e", // 1424182926
    "transactions": [{ ... }, { ... }, ...],
    "uncles": [
      "0x1606e5...",
      "0xd5145a9..."
    ]
  }
}
```

***

### eth_getBlockTransactionCountByHash

Returns the number of transactions in a block from a block matching the given block hash.

#### Parameters

0. `Hash` - 32 Bytes - hash of a block.

```js
params: ["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"]
```

#### Returns

- `Quantity` - integer of the number of transactions in this block.

#### Example

Request
```bash
curl --data '{"method":"eth_getBlockTransactionCountByHash","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xb" // 11
}
```

***

### eth_getBlockTransactionCountByNumber

Returns the number of transactions in a block from a block matching the given block number.

#### Parameters

0. `Quantity` or `Tag` - integer of a block number, or the string `'earliest'`, `'latest'` or `'pending'`, as in the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0xe8" // 232
]
```

#### Returns

- `Quantity` - integer of the number of transactions in this block.

#### Example

Request
```bash
curl --data '{"method":"eth_getBlockTransactionCountByNumber","params":["0xe8"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xa" // 10
}
```

***

### eth_getCode

Returns code at a given address.

#### Parameters

0. `Address` - 20 Bytes - address.
0. `Quantity` or `Tag` - integer block number, or the string `'latest'`, `'earliest'` or `'pending'`, see the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
  "0x2" // 2
]
```

#### Returns

- `Data` - the code from the given address.

#### Example

Request
```bash
curl --data '{"method":"eth_getCode","params":["0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b","0x2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
}
```

***

### eth_getFilterChanges

Polling method for a filter, which returns an array of logs which occurred since last poll.

#### Parameters

0. `Quantity` - The filter id.

```js
params: [
  "0x16" // 22
]
```

#### Returns

- `Array` - Array of log objects, or an empty array if nothing has changed since last poll.

#### Example

Request
```bash
curl --data '{"method":"eth_getFilterChanges","params":["0x16"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    {
      "logIndex": "0x1", // 1
      "blockNumber": "0x1b4", // 436
      "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
      "transactionHash": "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
      "transactionIndex": "0x0", // 0
      "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
      "data": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
    },
    ...
  ]
}
```

***

### eth_getFilterLogs

Returns an array of all logs matching filter with given id.

#### Parameters

0. `Quantity` - The filter id.

```js
params: [
  "0x16" // 22
]
```

#### Returns

- See [eth_getFilterChanges](#eth_getfilterchanges)

#### Example

Request
```bash
curl --data '{"method":"eth_getFilterLogs","params":["0x16"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

***

### eth_getLogs

Returns an array of all logs matching a given filter object.

#### Parameters

0. `Object` - The filter object, see [eth_newFilter parameters](#eth_newfilter).

```js
params: [{
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]
}]
```

#### Returns

- See [eth_getFilterChanges](#eth_getfilterchanges)

#### Example

Request
```bash
curl --data '{"method":"eth_getLogs","params":[{"topics":["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

***

### eth_getStorageAt

Returns the value from a storage position at a given address.

#### Parameters

0. `Address` - 20 Bytes - address of the storage.
0. `Quantity` - integer of the position in the storage.
0. `Quantity` or `Tag` - (optional) integer block number, or the string `'latest'`, `'earliest'` or `'pending'`, see the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "0x0", // 0
  "0x2" // 2
]
```

#### Returns

- `Data` - the value at this storage position.

#### Example

Request
```bash
curl --data '{"method":"eth_getStorageAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","0x0","0x2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x0000000000000000000000000000000000000000000000000000000000000003"
}
```

***

### eth_getTransactionByBlockHashAndIndex

Returns information about a transaction by block hash and transaction index position.

#### Parameters

0. `Hash` - hash of a block.
0. `Quantity` - integer of the transaction index position.

```js
params: [
  "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
  "0x0" // 0
]
```

#### Returns

- `Object` - A transaction object, or `null` when no transaction was found:
    - `hash`:   `Hash` - 32 Bytes - hash of the transaction.
    - `nonce`:   `Quantity` - the number of transactions made by the sender prior to this one.
    - `blockHash`:   `Hash` - 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
    - `blockNumber`:   `Quantity` or `Tag` - block number where this transaction was in. `null` when its pending.
    - `transactionIndex`:   `Quantity` - integer of the transactions index position in the block. `null` when its pending.
    - `from`:   `Address` - 20 Bytes - address of the sender.
    - `to`:   `Address` - 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
    - `value`:   `Quantity` - value transferred in Wei.
    - `gasPrice`:   `Quantity` - gas price provided by the sender in Wei.
    - `gas`:   `Quantity` - gas provided by the sender.
    - `input`:   `Data` - the data send along with the transaction.
    - `v`:   `Quantity` - the standardised V field of the signature.
    - `standardV`:   `Quantity` - the standardised V field of the signature (0 or 1).
    - `r`:   `Quantity` - the R field of the signature.
    - `raw`:   `Data` - raw transaction data
    - `publicKey`:   `Hash` - public key of the signer.
    - `chainId`:   `Quantity` - the chain id of the transaction, if any.
    - `creates`:   `Hash` - creates contract hash
    - `condition`:   `Object` - (optional) conditional submission, Block number in `block` or timestamp in `time` or `null`. (parity-feature)

#### Example

Request
```bash
curl --data '{"method":"eth_getTransactionByBlockHashAndIndex","params":["0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331","0x0"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "hash": "0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "nonce": "0x0", // 0
    "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
    "blockNumber": "0x15df", // 5599
    "transactionIndex": "0x1", // 1
    "from": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "to": "0x853f43d8a49eeb85d32cf465507dd71d507100c1",
    "value": "0x7f110", // 520464
    "gas": "0x7f110", // 520464
    "gasPrice": "0x09184e72a000",
    "input": "0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360"
  }
}
```

***

### eth_getTransactionByBlockNumberAndIndex

Returns information about a transaction by block number and transaction index position.

#### Parameters

0. `Quantity` or `Tag` - a block number, or the string `'earliest'`, `'latest'` or `'pending'`, as in the [default block parameter](#the-default-block-parameter).
0. `Quantity` - The transaction index position.

```js
params: [
  "0x29c", // 668
  "0x0" // 0
]
```

#### Returns

- `Object` - A block object, or `null` when no block was found.
    - `number`:   `Quantity` - The block number. `null` when its pending block
    - `hash`:   `Hash` - 32 Bytes - hash of the block. `null` when its pending block
    - `parentHash`:   `Hash` - 32 Bytes - hash of the parent block
    - `nonce`:   `Data` - 8 Bytes - hash of the generated proof-of-work. `null` when its pending block. Missing in case of PoA.
    - `sha3Uncles`:   `Data` - 32 Bytes - SHA3 of the uncles data in the block
    - `logsBloom`:   `Data` - 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block
    - `transactionsRoot`:   `Data` - 32 Bytes - the root of the transaction trie of the block
    - `stateRoot`:   `Data` - 32 Bytes - the root of the final state trie of the block
    - `receiptsRoot`:   `Data` - 32 Bytes - the root of the receipts trie of the block
    - `author`:   `Address` - 20 Bytes - the address of the author of the block (the beneficiary to whom the mining rewards were given)
    - `miner`:   `Address` - 20 Bytes - alias of 'author'
    - `difficulty`:   `Quantity` - integer of the difficulty for this block
    - `totalDifficulty`:   `Quantity` - integer of the total difficulty of the chain until this block
    - `extraData`:   `Data` - the 'extra data' field of this block
    - `size`:   `Quantity` - integer the size of this block in bytes
    - `gasLimit`:   `Quantity` - the maximum gas allowed in this block
    - `gasUsed`:   `Quantity` - the total used gas by all transactions in this block
    - `timestamp`:   `Quantity` - the unix timestamp for when the block was collated
    - `transactions`:   `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter
    - `uncles`:   `Array` - Array of uncle hashes

#### Example

Request
```bash
curl --data '{"method":"eth_getTransactionByBlockNumberAndIndex","params":["0x29c","0x0"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "number": "0x1b4", // 436
    "hash": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "parentHash": "0x9646252be9520f6e71339a8df9c55e4d7619deeb018d2a3f2d21fc165dde5eb5",
    "sealFields": [
      "0xe04d296d2460cfb8472af2c5fd05b5a214109c25688d3704aed5484f9a7792f2",
      "0x0000000000000042"
    ],
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "logsBloom": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "stateRoot": "0xd5855eb08b3387c0af375e9cdb6acfc05eb8f519e419b874b6ff2ffda7ed1dff",
    "miner": "0x4e65fda2159562a496f9f3522f89122a3088497a",
    "difficulty": "0x27f07", // 163591
    "totalDifficulty": "0x27f07", // 163591
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "size": "0x27f07", // 163591
    "gasLimit": "0x9f759", // 653145
    "minGasPrice": "0x9f759", // 653145
    "gasUsed": "0x9f759", // 653145
    "timestamp": "0x54e34e8e", // 1424182926
    "transactions": [{ ... }, { ... }, ...],
    "uncles": [
      "0x1606e5...",
      "0xd5145a9..."
    ]
  }
}
```

***

### eth_getTransactionByHash

Returns the information about a transaction requested by transaction hash.

#### Parameters

0. `Hash` - 32 Bytes - hash of a transaction.

```js
params: ["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"]
```

#### Returns

- `Object` - A transaction object, or `null` when no transaction was found:
    - `hash`:   `Hash` - 32 Bytes - hash of the transaction.
    - `nonce`:   `Quantity` - the number of transactions made by the sender prior to this one.
    - `blockHash`:   `Hash` - 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
    - `blockNumber`:   `Quantity` or `Tag` - block number where this transaction was in. `null` when its pending.
    - `transactionIndex`:   `Quantity` - integer of the transactions index position in the block. `null` when its pending.
    - `from`:   `Address` - 20 Bytes - address of the sender.
    - `to`:   `Address` - 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
    - `value`:   `Quantity` - value transferred in Wei.
    - `gasPrice`:   `Quantity` - gas price provided by the sender in Wei.
    - `gas`:   `Quantity` - gas provided by the sender.
    - `input`:   `Data` - the data send along with the transaction.
    - `v`:   `Quantity` - the standardised V field of the signature.
    - `standardV`:   `Quantity` - the standardised V field of the signature (0 or 1).
    - `r`:   `Quantity` - the R field of the signature.
    - `raw`:   `Data` - raw transaction data
    - `publicKey`:   `Hash` - public key of the signer.
    - `chainId`:   `Quantity` - the chain id of the transaction, if any.
    - `creates`:   `Hash` - creates contract hash
    - `condition`:   `Object` - (optional) conditional submission, Block number in `block` or timestamp in `time` or `null`. (parity-feature)

#### Example

Request
```bash
curl --data '{"method":"eth_getTransactionByHash","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "hash": "0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "nonce": "0x0", // 0
    "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
    "blockNumber": "0x15df", // 5599
    "transactionIndex": "0x1", // 1
    "from": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "to": "0x853f43d8a49eeb85d32cf465507dd71d507100c1",
    "value": "0x7f110", // 520464
    "gas": "0x7f110", // 520464
    "gasPrice": "0x09184e72a000",
    "input": "0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360"
  }
}
```

***

### eth_getTransactionCount

Returns the number of transactions *sent* from an address.

#### Parameters

0. `Address` - 20 Bytes - address.
0. `Quantity` or `Tag` - (optional) integer block number, or the string `'latest'`, `'earliest'` or `'pending'`, see the [default block parameter](#the-default-block-parameter).

```js
params: ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
```

#### Returns

- `Quantity` - integer of the number of transactions send from this address.

#### Example

Request
```bash
curl --data '{"method":"eth_getTransactionCount","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

### eth_getTransactionReceipt

Returns the receipt of a transaction by transaction hash.

**Note** That the receipt is available even for pending transactions.

#### Parameters

0. `Hash` - hash of a transaction.

```js
params: ["0x444172bef57ad978655171a8af2cfd89baa02a97fcb773067aef7794d6913374"]
```

#### Returns

- `Object` - A transaction receipt object, or `null` when no receipt was found:
    - `blockHash`:   `Hash` - 32 Bytes - hash of the block where this transaction was in.
    - `blockNumber`:   `Quantity` or `Tag` - block number where this transaction was in.
    - `contractAddress`:   `Address` - 20 Bytes - The contract address created, if the transaction was a contract creation, otherwise `null`.
    - `cumulativeGasUsed`:   `Quantity` - The total amount of gas used when this transaction was executed in the block.
    - `from`:   `Address` - 20 Bytes - The address of the sender.
    - `to`:   `Address` - 20 Bytes - The address of the receiver. null when it’s a contract creation transaction.
    - `gasUsed`:   `Quantity` - The amount of gas used by this specific transaction alone.
    - `logs`:   `Array` - Array of log objects, which this transaction generated.
    - `logsBloom`:   `Hash` - 256 Bytes - A bloom filter of logs/events generated by contracts during transaction execution. Used to efficiently rule out transactions without expected logs.
    - `root`:   `Hash` - 32 Bytes - Merkle root of the state trie after the transaction has been executed (optional after Byzantium hard fork [EIP609](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-609.md))
    - `status`:   `Quantity` - `0x0` indicates transaction failure , `0x1` indicates transaction success. Set for blocks mined after Byzantium hard fork [EIP609](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-609.md), `null` before.
    - `transactionHash`:   `Hash` - 32 Bytes - hash of the transaction.
    - `transactionIndex`:   `Quantity` - Integer of the transactions index position in the block.

#### Example

Request
```bash
curl --data '{"method":"eth_getTransactionReceipt","params":["0x444172bef57ad978655171a8af2cfd89baa02a97fcb773067aef7794d6913374"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "blockHash": "0x67c0303244ae4beeec329e0c66198e8db8938a94d15a366c7514626528abfc8c",
    "blockNumber": "0x6914b0",
    "contractAddress": "0x471a8bf3fd0dfbe20658a97155388cec674190bf", // or null, if none was created
    "from": "0xc931d93e97ab07fe42d923478ba2465f2",
    "to": null, // value is null because this example transaction is a contract creation
    "cumulativeGasUsed": "0x158e33",
    "gasUsed": "0xba2e6",
    "logs": [], // logs as returned by eth_getFilterLogs, etc.
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "root": null,
    "status": "0x1",
    "transactionHash": "0x444172bef57ad978655171a8af2cfd89baa02a97fcb773067aef7794d6913374",
    "transactionIndex": "0x4"
  }
}
```

***

### eth_getUncleByBlockHashAndIndex

Returns information about a uncle of a block by hash and uncle index position.

**Note:** An uncle doesn't contain individual transactions.

#### Parameters

0. `Hash` - Hash of a block.
0. `Quantity` - The uncle's index position.

```js
params: [
  "0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
  "0x0" // 0
]
```

#### Returns

- `Object` - A block object, or `null` when no block was found.
    - `number`:   `Quantity` - The block number. `null` when its pending block
    - `hash`:   `Hash` - 32 Bytes - hash of the block. `null` when its pending block
    - `parentHash`:   `Hash` - 32 Bytes - hash of the parent block
    - `nonce`:   `Data` - 8 Bytes - hash of the generated proof-of-work. `null` when its pending block. Missing in case of PoA.
    - `sha3Uncles`:   `Data` - 32 Bytes - SHA3 of the uncles data in the block
    - `logsBloom`:   `Data` - 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block
    - `transactionsRoot`:   `Data` - 32 Bytes - the root of the transaction trie of the block
    - `stateRoot`:   `Data` - 32 Bytes - the root of the final state trie of the block
    - `receiptsRoot`:   `Data` - 32 Bytes - the root of the receipts trie of the block
    - `author`:   `Address` - 20 Bytes - the address of the author of the block (the beneficiary to whom the mining rewards were given)
    - `miner`:   `Address` - 20 Bytes - alias of 'author'
    - `difficulty`:   `Quantity` - integer of the difficulty for this block
    - `totalDifficulty`:   `Quantity` - integer of the total difficulty of the chain until this block
    - `extraData`:   `Data` - the 'extra data' field of this block
    - `size`:   `Quantity` - integer the size of this block in bytes
    - `gasLimit`:   `Quantity` - the maximum gas allowed in this block
    - `gasUsed`:   `Quantity` - the total used gas by all transactions in this block
    - `timestamp`:   `Quantity` - the unix timestamp for when the block was collated
    - `transactions`:   `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter
    - `uncles`:   `Array` - Array of uncle hashes

#### Example

Request
```bash
curl --data '{"method":"eth_getUncleByBlockHashAndIndex","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b","0x0"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "number": "0x1b4", // 436
    "hash": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "parentHash": "0x9646252be9520f6e71339a8df9c55e4d7619deeb018d2a3f2d21fc165dde5eb5",
    "sealFields": [
      "0xe04d296d2460cfb8472af2c5fd05b5a214109c25688d3704aed5484f9a7792f2",
      "0x0000000000000042"
    ],
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "logsBloom": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "stateRoot": "0xd5855eb08b3387c0af375e9cdb6acfc05eb8f519e419b874b6ff2ffda7ed1dff",
    "miner": "0x4e65fda2159562a496f9f3522f89122a3088497a",
    "difficulty": "0x27f07", // 163591
    "totalDifficulty": "0x27f07", // 163591
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "size": "0x27f07", // 163591
    "gasLimit": "0x9f759", // 653145
    "minGasPrice": "0x9f759", // 653145
    "gasUsed": "0x9f759", // 653145
    "timestamp": "0x54e34e8e", // 1424182926
    "transactions": [{ ... }, { ... }, ...],
    "uncles": [
      "0x1606e5...",
      "0xd5145a9..."
    ]
  }
}
```

***

### eth_getUncleByBlockNumberAndIndex

Returns information about a uncle of a block by number and uncle index position.

**Note:** An uncle doesn't contain individual transactions.

#### Parameters

0. `Quantity` or `Tag` - a block number, or the string `'earliest'`, `'latest'` or `'pending'`, as in the [default block parameter](#the-default-block-parameter).
0. `Quantity` - The uncle's index position.

```js
params: [
  "0x29c", // 668
  "0x0" // 0
]
```

#### Returns

- `Object` - A block object, or `null` when no block was found.
    - `number`:   `Quantity` - The block number. `null` when its pending block
    - `hash`:   `Hash` - 32 Bytes - hash of the block. `null` when its pending block
    - `parentHash`:   `Hash` - 32 Bytes - hash of the parent block
    - `nonce`:   `Data` - 8 Bytes - hash of the generated proof-of-work. `null` when its pending block. Missing in case of PoA.
    - `sha3Uncles`:   `Data` - 32 Bytes - SHA3 of the uncles data in the block
    - `logsBloom`:   `Data` - 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block
    - `transactionsRoot`:   `Data` - 32 Bytes - the root of the transaction trie of the block
    - `stateRoot`:   `Data` - 32 Bytes - the root of the final state trie of the block
    - `receiptsRoot`:   `Data` - 32 Bytes - the root of the receipts trie of the block
    - `author`:   `Address` - 20 Bytes - the address of the author of the block (the beneficiary to whom the mining rewards were given)
    - `miner`:   `Address` - 20 Bytes - alias of 'author'
    - `difficulty`:   `Quantity` - integer of the difficulty for this block
    - `totalDifficulty`:   `Quantity` - integer of the total difficulty of the chain until this block
    - `extraData`:   `Data` - the 'extra data' field of this block
    - `size`:   `Quantity` - integer the size of this block in bytes
    - `gasLimit`:   `Quantity` - the maximum gas allowed in this block
    - `gasUsed`:   `Quantity` - the total used gas by all transactions in this block
    - `timestamp`:   `Quantity` - the unix timestamp for when the block was collated
    - `transactions`:   `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter
    - `uncles`:   `Array` - Array of uncle hashes

#### Example

Request
```bash
curl --data '{"method":"eth_getUncleByBlockNumberAndIndex","params":["0x29c","0x0"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "number": "0x1b4", // 436
    "hash": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "parentHash": "0x9646252be9520f6e71339a8df9c55e4d7619deeb018d2a3f2d21fc165dde5eb5",
    "sealFields": [
      "0xe04d296d2460cfb8472af2c5fd05b5a214109c25688d3704aed5484f9a7792f2",
      "0x0000000000000042"
    ],
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "logsBloom": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "stateRoot": "0xd5855eb08b3387c0af375e9cdb6acfc05eb8f519e419b874b6ff2ffda7ed1dff",
    "miner": "0x4e65fda2159562a496f9f3522f89122a3088497a",
    "difficulty": "0x27f07", // 163591
    "totalDifficulty": "0x27f07", // 163591
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "size": "0x27f07", // 163591
    "gasLimit": "0x9f759", // 653145
    "minGasPrice": "0x9f759", // 653145
    "gasUsed": "0x9f759", // 653145
    "timestamp": "0x54e34e8e", // 1424182926
    "transactions": [{ ... }, { ... }, ...],
    "uncles": [
      "0x1606e5...",
      "0xd5145a9..."
    ]
  }
}
```

***

### eth_getUncleCountByBlockHash

Returns the number of uncles in a block from a block matching the given block hash.

#### Parameters

0. `Hash` - 32 Bytes - hash of a block.

```js
params: ["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"]
```

#### Returns

- `Quantity` - integer of the number of uncles in this block.

#### Example

Request
```bash
curl --data '{"method":"eth_getUncleCountByBlockHash","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x0" // 0
}
```

***

### eth_getUncleCountByBlockNumber

Returns the number of uncles in a block from a block matching the given block number.

#### Parameters

0. `Quantity` or `Tag` - integer of a block number, or the string 'latest', 'earliest' or 'pending', see the [default block parameter](#the-default-block-parameter).

```js
params: [
  "0xe8" // 232
]
```

#### Returns

- `Quantity` - integer of the number of uncles in this block.

#### Example

Request
```bash
curl --data '{"method":"eth_getUncleCountByBlockNumber","params":["0xe8"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

### eth_getWork

Returns the hash of the current block, the seedHash, and the boundary condition to be met ("target").

#### Parameters

None

#### Returns

- `Array` - Array with the following properties:
  - `Data`, 32 Bytes - current block header pow-hash.
  - `Data`, 32 Bytes - the seed hash used for the DAG.
  - `Data`, 32 Bytes - the boundary condition ("target"), 2^256 / difficulty.
  - `Quantity`, the current block number.

#### Example

Request
```bash
curl --data '{"method":"eth_getWork","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
    "0x5EED00000000000000000000000000005EED0000000000000000000000000000",
    "0xd1ff1c01710000000000000000000000d1ff1c01710000000000000000000000",
    "0x1" // 1
  ]
}
```

***

### eth_hashrate

Returns the number of hashes per second that the node is mining with.

#### Parameters

None

#### Returns

- `Quantity` - number of hashes per second.

#### Example

Request
```bash
curl --data '{"method":"eth_hashrate","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x38a" // 906
}
```

***

### eth_mining

Returns `true` if client is actively mining new blocks.

#### Parameters

None

#### Returns

- `Boolean` - `true` of the client is mining, otherwise `false`.

#### Example

Request
```bash
curl --data '{"method":"eth_mining","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### eth_newBlockFilter

Creates a filter in the node, to notify when a new block arrives.
To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).

#### Parameters

None

#### Returns

- `Quantity` - A filter id.

#### Example

Request
```bash
curl --data '{"method":"eth_newBlockFilter","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

### eth_newFilter

Creates a filter object, based on filter options, to notify when the state changes (logs).
To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).

##### A note on specifying topic filters:
Topics are order-dependent. A transaction with a log with topics [A, B] will be matched by the following topic filters:
* `[]` "anything"
* `[A]` "A in first position (and anything after)"
* `[null, B]` "anything in first position AND B in second position (and anything after)"
* `[A, B]` "A in first position AND B in second position (and anything after)"
* `[[A, B], [A, B]]` "(A OR B) in first position AND (A OR B) in second position (and anything after)"

#### Parameters

0. `Object` - The filter options:
    - `fromBlock`:   `Quantity` or `Tag` - (optional) (default: `latest`) Integer block number, or `'latest'` for the last mined block or `'pending'`, `'earliest'` for not yet mined transactions.
    - `toBlock`:   `Quantity` or `Tag` - (optional) (default: `latest`) Integer block number, or `'latest'` for the last mined block or `'pending'`, `'earliest'` for not yet mined transactions.
    - `address`:   `Address` - (optional) 20 Bytes - Contract address or a list of addresses from which logs should originate.
    - `topics`:   `Array` - (optional) Array of 32 Bytes `Data` topics. Topics are order-dependent. It's possible to pass in `null` to match any topic, or a subarray of multiple topics of which one should be matching.
    - `limit`:   `Quantity` - (optional) The maximum number of entries to retrieve (latest first).

```js
params: [{
  "fromBlock": "0x1", // 1
  "toBlock": "0x2", // 2
  "address": "0x8888f1f195afa192cfee860698584c030f4c9db1",
  "topics": [
    "0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b", // This topic in first position
    null, // Any topic in second position
    [
      "0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b",
      "0x000000000000000000000000aff3454fce5edbc8cca8697c15331677e6ebccc"
    ] // Either topic of the two in third position
  ] // ... and anything after
}]
```

#### Returns

- `Quantity` - The filter id.

#### Example

Request
```bash
curl --data '{"method":"eth_newFilter","params":[{"fromBlock":"0x1","toBlock":"0x2","address":"0x8888f1f195afa192cfee860698584c030f4c9db1","topics":["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b",null,["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b","0x000000000000000000000000aff3454fce5edbc8cca8697c15331677e6ebccc"]]}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

### eth_newPendingTransactionFilter

Creates a filter in the node, to notify when new pending transactions arrive.

To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).

#### Parameters

None

#### Returns

- `Quantity` - A filter id.

#### Example

Request
```bash
curl --data '{"method":"eth_newPendingTransactionFilter","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

### eth_protocolVersion

Returns the current ethereum protocol version.

#### Parameters

None

#### Returns

- `String` - The current ethereum protocol version.

#### Example

Request
```bash
curl --data '{"method":"eth_protocolVersion","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x63" // 99
}
```

***

### eth_sendRawTransaction

Creates new message call transaction or a contract creation for signed transactions.

**Note:** `eth_submitTransaction` is an alias of this method.

#### Parameters

0. `Data` - The signed transaction data.

```js
params: ["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"]
```

#### Returns

- `Hash` - 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available

Use [eth_getTransactionReceipt](#eth_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

#### Example

Request
```bash
curl --data '{"method":"eth_sendRawTransaction","params":["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

***

### eth_sendTransaction

Creates new message call transaction or a contract creation, if the data field contains code.

#### Parameters

0. `Object` - The transaction object.
    - `from`:   `Address` - 20 Bytes - The address the transaction is send from.
    - `to`:   `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`:   `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

```js
params: [{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
  "gas": "0x76c0", // 30400
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

#### Returns

- `Hash` - 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

Use [eth_getTransactionReceipt](#eth_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

#### Example

Request
```bash
curl --data '{"method":"eth_sendTransaction","params":[{"from":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","to":"0xd46e8dd67c5d32be8058bb8eb970870f07244567","gas":"0x76c0","gasPrice":"0x9184e72a000","value":"0x9184e72a","data":"0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

***

### eth_sign

The sign method calculates an Ethereum specific signature with: `sign(keccak256("Ethereum Signed Message:
" + len(message) + message)))`.

#### Parameters

0. `Address` - 20 Bytes - address.
0. `Data` - Data which hash to sign.

```js
params: [
  "0xcd2a3d9f938e13cd947ec05abc7fe734df8dd826",
  "0x5363686f6f6c627573" // Schoolbus
]
```

#### Returns

- `Data` - Signed data.

#### Example

Request
```bash
curl --data '{"method":"eth_sign","params":["0xcd2a3d9f938e13cd947ec05abc7fe734df8dd826","0x5363686f6f6c627573"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xb1092cb5b23c2aa55e5b5787729c6be812509376de99a52bea2b41e5a5f8601c5641e74d01e4493c17bf1ef8b179c49362b2c721222128d58422a539310c6ecd1b"
}
```

***

### eth_signTransaction

Signs transactions without dispatching it to the network. It can be later submitted using [eth_sendRawTransaction](#eth_sendrawtransaction).

#### Parameters

0. `Object` - Transaction object, see [eth_sendTransaction](#eth_sendTransaction).
    - `from`:   `Address` - 20 Bytes - The address the transaction is send from.
    - `to`:   `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`:   `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

#### Returns

- `Object` - Signed transaction and it's details:
    - `raw`:   `Data` - The signed, RLP encoded transaction.
    - `tx`:   `Object` - Transaction object:
        - `hash`:   `Hash` - 32 Bytes - hash of the transaction.
        - `nonce`:   `Quantity` - the number of transactions made by the sender prior to this one.
        - `blockHash`:   `Hash` - 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
        - `blockNumber`:   `Quantity` or `Tag` - block number where this transaction was in. `null` when its pending.
        - `transactionIndex`:   `Quantity` - integer of the transactions index position in the block. `null` when its pending.
        - `from`:   `Address` - 20 Bytes - address of the sender.
        - `to`:   `Address` - 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
        - `value`:   `Quantity` - value transferred in Wei.
        - `gasPrice`:   `Quantity` - gas price provided by the sender in Wei.
        - `gas`:   `Quantity` - gas provided by the sender.
        - `input`:   `Data` - the data send along with the transaction.
        - `v`:   `Quantity` - the standardised V field of the signature.
        - `standard_v`:   `Quantity` - the standardised V field of the signature (0 or 1).
        - `r`:   `Quantity` - the R field of the signature.
        - `raw`:   `Data` - raw transaction data
        - `publicKey`:   `Hash` - public key of the signer.
        - `chainId`:   `Quantity` - the chain id of the transaction, if any.
        - `creates`:   `Hash` - creates contract hash
        - `condition`:   `Object` - (optional) conditional submission, Block number in `block` or timestamp in `time` or `null`. (parity-feature)

#### Example

Request
```bash
curl --data '{"method":"eth_signTransaction","params":[{ ... }],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "raw": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
    "tx": {
      "hash": "0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
      "nonce": "0x0", // 0
      "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
      "blockNumber": "0x15df", // 5599
      "transactionIndex": "0x1", // 1
      "from": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
      "to": "0x853f43d8a49eeb85d32cf465507dd71d507100c1",
      "value": "0x7f110", // 520464
      "gas": "0x7f110", // 520464
      "gasPrice": "0x09184e72a000",
      "input": "0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360"
    }
  }
}
```

***

### eth_submitHashrate

Used for submitting mining hashrate.

#### Parameters

0. `Data` - a hexadecimal string representation (32 bytes) of the hash rate.
0. `Data` - A random hexadecimal(32 bytes) ID identifying the client.

```js
params: [
  "0x0000000000000000000000000000000000000000000000000000000000500000",
  "0x59daa26581d0acd1fce254fb7e85952f4c09d0915afd33d3886cd914bc7d283c"
]
```

#### Returns

- `Boolean` - `true` if submitting went through succesfully and `false` otherwise.

#### Example

Request
```bash
curl --data '{"method":"eth_submitHashrate","params":["0x0000000000000000000000000000000000000000000000000000000000500000","0x59daa26581d0acd1fce254fb7e85952f4c09d0915afd33d3886cd914bc7d283c"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### eth_submitWork

Used for submitting a proof-of-work solution.

#### Parameters

0. `Data` - 8 Bytes - The nonce found (64 bits).
0. `Data` - 32 Bytes - The header's pow-hash (256 bits)
0. `Data` - 32 Bytes - The mix digest (256 bits).

```js
params: [
  "0x0000000000000001",
  "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
  "0xD1FE5700000000000000000000000000D1FE5700000000000000000000000000"
]
```

#### Returns

- `Boolean` - `true` if the provided solution is valid, otherwise `false`.

#### Example

Request
```bash
curl --data '{"method":"eth_submitWork","params":["0x0000000000000001","0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef","0xD1FE5700000000000000000000000000D1FE5700000000000000000000000000"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### eth_syncing

Returns an object with data about the sync status or `false`.

#### Parameters

None

#### Returns

- `Object` - An object with sync status data or `FALSE`, when not syncing.
    - `startingBlock`:   `Quantity` - The block at which the import started (will only be reset, after the sync reached this head)
    - `currentBlock`:   `Quantity` - The current block, same as eth_blockNumber
    - `highestBlock`:   `Quantity` - The estimated highest block
    - `blockGap`:   `Array` - Array of "first", "last", such that [first, last) are all missing from the chain
    - `warpChunksAmount`:   `Quantity` - Total amount of snapshot chunks
    - `warpChunksProcessed`:   `Quantity` - Total amount of snapshot chunks processed

#### Example

Request
```bash
curl --data '{"method":"eth_syncing","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "startingBlock": "0x384", // 900
    "currentBlock": "0x386", // 902
    "highestBlock": "0x454" // 1108
  } // Or `false` when not syncing
}
```

***

### eth_uninstallFilter

Uninstalls a filter with given id. Should always be called when watch is no longer needed.
Additonally Filters timeout when they aren't requested with [eth_getFilterChanges](#eth_getfilterchanges) for a period of time.

#### Parameters

0. `Quantity` - The filter id.

```js
params: [
  "0xb" // 11
]
```

#### Returns

- `Boolean` - `true` if the filter was successfully uninstalled, otherwise `false`.

#### Example

Request
```bash
curl --data '{"method":"eth_uninstallFilter","params":["0xb"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

