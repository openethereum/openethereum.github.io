# The `personal` Module

## JSON-RPC methods

- [personal_listAccounts](#personal_listaccounts)
- [personal_newAccount](#personal_newaccount)
- [personal_sendTransaction](#personal_sendtransaction)
- [personal_unlockAccount](#personal_unlockaccount)

## JSON-RPC API Reference

### personal_listAccounts

Lists all stored accounts.

#### Parameters

None

#### Returns

- `Array` - A list of 20 byte account identifiers.

#### Example

Request
```bash
curl --data '{"method":"personal_listAccounts","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "0x7bf87721a96849d168de02fd6ea5986a3a147383",
    "0xca807a90fd64deed760fb98bf0869b475c469348"
  ]
}
```

***

### personal_newAccount

Creates new account.

**Note:** it becomes the new current unlocked account. There can only be one unlocked account at a time.

#### Parameters

0. `String` - Password for the new account.

```js
params: ["hunter2"]
```

#### Returns

- `Address` - 20 Bytes - The identifier of the new account.

#### Example

Request
```bash
curl --data '{"method":"personal_newAccount","params":["hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x8f0227d45853a50eefd48dd4fec25d5b3fd2295e"
}
```

***

### personal_sendTransaction

Sends transaction and signs it in a single call. The account does not need to be unlocked to make this call, and will not be left unlocked after.

#### Parameters

0. `Object` - The transaction object
    - `from`: `Address` - 20 Bytes - The address the transaction is send from.
    - `to`: `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`: `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`: `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`: `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`: `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`: `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`: `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.
0. `String` - Passphrase to unlock the `from` account.

```js
params: [
  {
    "from": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "to": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
    "data": "0x41cd5add4fd13aedd64521e363ea279923575ff39718065d38bd46f0e6632e8e",
    "value": "0x186a0"
  },
  "hunter2"
]
```

#### Returns

- `Data` - 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available

#### Example

Request
```bash
curl --data '{"method":"personal_sendTransaction","params":[{"from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1","to":"0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b","data":"0x41cd5add4fd13aedd64521e363ea279923575ff39718065d38bd46f0e6632e8e","value":"0x186a0"},"hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x62e05075829655752e146a129a044ad72e95ce33e48ff48118b697e15e7b41e4"
}
```

***

### personal_unlockAccount

Unlocks specified account for use.

If permanent unlocking is disabled (the default) then the duration argument will be ignored, and the account will be unlocked for a single signing. With permanent locking enabled, the duration sets the number of seconds to hold the account open for. It will default to 300 seconds. Passing 0 unlocks the account indefinitely.

There can only be one unlocked account at a time.

#### Parameters

0. `Address` - 20 Bytes - The address of the account to unlock.
0. `String` - Passphrase to unlock the account.
0. `Quantity` - (default: `300`) Integer or `null` - Duration in seconds how long the account should remain unlocked for.

```js
params: [
  "0x8f0227d45853a50eefd48dd4fec25d5b3fd2295e",
  "hunter2",
  null
]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
curl --data '{"method":"personal_unlockAccount","params":["0x8f0227d45853a50eefd48dd4fec25d5b3fd2295e","hunter2",null],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

