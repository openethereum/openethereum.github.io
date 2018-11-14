---
title: The `personal` Module
---

## JSON-RPC methods

- [personal_ecRecover](#personal_ecrecover)
- [personal_listAccounts](#personal_listaccounts)
- [personal_newAccount](#personal_newaccount)
- [personal_sendTransaction](#personal_sendtransaction)
- [personal_sign](#personal_sign)
- [personal_sign191](#personal_sign191)
- [personal_signTransaction](#personal_signtransaction)
- [personal_signTypedData](#personal_signtypeddata)
- [personal_unlockAccount](#personal_unlockaccount)

## JSON-RPC API Reference

### personal_ecRecover

Returns the address associated with the private key that was used to calculate the signature in `personal_sign`.

#### Parameters

0. `Data` - The data which hash was signed.
0. `Data` - Signed data.

```js
params: [
  "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
  "0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"
]
```

#### Returns

- `Address` - Address of the signer of the message.

#### Example

Request
```bash
curl --data '{"method":"personal_ecRecover","params":["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675","0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xb60e8dd61c5d32be8058bb8eb970870f07233155"
}
```

***

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
    - `from`:   `Address` - 20 Bytes - The address the transaction is send from.
    - `to`:   `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`:   `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.
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

### personal_sign

Calculates an Ethereum specific signature with: `sign(keccak256("Ethereum Signed Message: " + len(message) + message)))`.

#### Parameters

0. `Data` - The data to sign
0. `Address` - 20 Bytes - The address of the account to sign with
0. `String` - Passphrase to unlock the `from` account.

```js
params: [
  "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
  "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "hunter"
]
```

#### Returns

- `Data` - Signed data.

#### Example

Request
```bash
curl --data '{"method":"personal_sign","params":["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675","0xb60e8dd61c5d32be8058bb8eb970870f07233155","hunter"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"
}
```

***

### personal_sign191

EIP-191 compliant signing, allows signing of different data formats depending on the version specifier

#### Parameters

0. `String` - EIP-191 version specifier, `0x00` for pre-signed transactions, `0x01` for structured data(EIP712), `0x45` for personal messages
0. `EIP712` or `Data` or `PresignedTransaction` - Data to be signed, depending on the version specified in the first argument. `PresignedTransaction`, `EIP712` or `Data`
    - `EIP712`:   `Object` - EIP-712 compliant data structure, if version specified is `0x01`
        - `primaryType`:   `String` - name of the struct defined in `types` that is the same type as `message`
        - `domain`:   `Object` - EIP712Domain
            - `name`:   `String` - User readable name of signing domain, i.e. the name of the Dapp or the protocol
            - `verifyingContract`:   `Address` - Address of the contract that verifies the signed message
            - `chainId`:   `Integer` - chain id this signature is valid for to prevent chain replay attacks
            - `version`:   `Integer` - The current major version of the signing domain. Signatures from different versions are not compatible.
            - `salt`:   `Data` - Should be used as a last resort domain seperator
        - `message`:   `Object` - Structured message to be signed
        - `types`:   `Object` - type definitions for the `EIP712Domain` and the 'primaryType' as well as it's dependent types
    - `Data`:   `Data` - hashed message to sign, if version specified is `0x45`
    - `PresignedTransaction`:   `Object` - presigned transaction data, if version specified is `0x00`
        - `data`:   `Data` - Presigned Transaction data
        - `validator`:   `Address` - address of the contract that validates the presigned transaction
0. `Address` - 20 Bytes - The address of the account to sign with
0. `String` - The account password

```js
params: [
  "0x00",
  {
    "validator": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
    "data": "0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"
  },
  "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "password"
]
```

#### Returns

- `Data` - Signed data.

#### Example

Request
```bash
curl --data '{"method":"personal_sign191","params":["0x00",{"validator":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","data":"0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"},"0xb60e8dd61c5d32be8058bb8eb970870f07233155","password"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"
}
```

***

### personal_signTransaction

Signs a transaction without dispatching it to the network. It can later be submitted using `eth_sendRawTransaction`. The account does not need to be unlocked to make this call, and will not be left unlocked after.

#### Parameters

0. `Object` - The transaction object
    - `from`:   `Address` - 20 Bytes - The address the transaction is send from.
    - `to`:   `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`:   `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.
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

- `Object` - Signed transaction and its details:
    - `raw`:   `Data` - The signed, RLP encoded transaction.
    - `tx`:   `Object` - Transaction object.
        - `from`:   `Address` - 20 Bytes - The address the transaction is send from.
        - `to`:   `Address` - (optional) 20 Bytes - The address the transaction is directed to.
        - `gas`:   `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
        - `gasPrice`:   `Quantity` - (optional) Integer of the gas price used for each paid gas.
        - `value`:   `Quantity` - (optional) Integer of the value sent with this transaction.
        - `data`:   `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
        - `nonce`:   `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
        - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

#### Example

Request
```bash
curl --data '{"method":"personal_signTransaction","params":[{"from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1","to":"0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b","data":"0x41cd5add4fd13aedd64521e363ea279923575ff39718065d38bd46f0e6632e8e","value":"0x186a0"},"hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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
      "nonce": "0x0",
      "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
      "blockNumber": "0x15df",
      "transactionIndex": "0x1",
      "from": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
      "to": "0x853f43d8a49eeb85d32cf465507dd71d507100c1",
      "value": "0x7f110",
      "gas": "0x7f110",
      "gasPrice": "0x09184e72a000",
      "input": "0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360"
    }
  }
}
```

***

### personal_signTypedData

Hashes and signs typed structured data

#### Parameters

0. `Object` - EIP-712 compliant data structure to be signed
    - `primaryType`:   `String` - name of the struct defined in `types` that is the same type as `message`
    - `domain`:   `Object` - EIP712Domain
        - `name`:   `String` - User readable name of signing domain, i.e. the name of the Dapp or the protocol
        - `verifyingContract`:   `Address` - Address of the contract that verifies the signed message
        - `chainId`:   `Integer` - chain id this signature is valid for to prevent chain replay attacks
        - `version`:   `Integer` - The current major version of the signing domain. Signatures from different versions are not compatible.
        - `salt`:   `Data` - Should be used as a last resort domain seperator
    - `message`:   `Object` - Structured message to be signed
    - `types`:   `Object` - type definitions for the `EIP712Domain` and the 'primaryType' as well as it's dependent types
0. `Address` - 20 Bytes - The address of the account to sign with
0. `String` - The account password

```js
params: [
  {
    "types": {
      "EIP712Domain": [
        {
          "name": "name",
          "type": "string"
        },
        {
          "name": "version",
          "type": "string"
        },
        {
          "name": "chainId",
          "type": "uint256"
        },
        {
          "name": "verifyingContract",
          "type": "address"
        }
      ],
      "Person": [
        {
          "name": "name",
          "type": "string"
        },
        {
          "name": "wallet",
          "type": "address"
        }
      ],
      "Mail": [
        {
          "name": "from",
          "type": "Person"
        },
        {
          "name": "to",
          "type": "Person"
        },
        {
          "name": "contents",
          "type": "string"
        }
      ]
    },
    "primaryType": "Mail",
    "domain": {
      "name": "Ether Mail",
      "version": "1",
      "chainId": 1,
      "verifyingContract": "0xCcCCccccCCCCcCCCCCCcCcCccCcCCCcCcccccccC"
    },
    "message": {
      "from": {
        "name": "Cow",
        "wallet": "0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826"
      },
      "to": {
        "name": "Bob",
        "wallet": "0xbBbBBBBbbBBBbbbBbbBbbbbBBbBbbbbBbBbbBBbB"
      },
      "contents": "Hello, Bob!"
    }
  },
  "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "password"
]
```

#### Returns

- `Data` - Signed data.

#### Example

Request
```bash
curl --data '{"method":"personal_signTypedData","params":[{"types":{"EIP712Domain":[{"name":"name","type":"string"},{"name":"version","type":"string"},{"name":"chainId","type":"uint256"},{"name":"verifyingContract","type":"address"}],"Person":[{"name":"name","type":"string"},{"name":"wallet","type":"address"}],"Mail":[{"name":"from","type":"Person"},{"name":"to","type":"Person"},{"name":"contents","type":"string"}]},"primaryType":"Mail","domain":{"name":"Ether Mail","version":"1","chainId":1,"verifyingContract":"0xCcCCccccCCCCcCCCCCCcCcCccCcCCCcCcccccccC"},"message":{"from":{"name":"Cow","wallet":"0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826"},"to":{"name":"Bob","wallet":"0xbBbBBBBbbBBBbbbBbbBbbbbBBbBbbbbBbBbbBBbB"},"contents":"Hello, Bob!"}},"0xb60e8dd61c5d32be8058bb8eb970870f07233155","password"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"
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

