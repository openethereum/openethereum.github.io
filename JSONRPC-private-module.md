# The `private` Module

## JSON-RPC methods

- [private_call](#private_call)
- [private_sendTransaction](#private_sendtransaction)

## JSON-RPC API Reference

### private_call

Make an local call to the private contract (without its state modification).

#### Parameters

0. `Quantity` | `Tag` - integer block number, or the string `'latest'`, `'earliest'`, see the [default block parameter](#the-default-block-parameter).
0. `Object` - The transaction object corresponding to the call
    - `from`: `Address` - 20 Bytes - The address the transaction is send from.
    - `to`: `Address` - (optional) 20 Bytes - The address the transaction is directed to.
    - `gas`: `Quantity` - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`: `Quantity` - (optional) Integer of the gas price used for each paid gas.
    - `value`: `Quantity` - (optional) Integer of the value sent with this transaction.
    - `data`: `Data` - (optional) 4 byte hash of the method signature followed by encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).
    - `nonce`: `Quantity` - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
    - `condition`: `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.

```js
params: [
  "0x2", // 2
  {
    "from": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "to": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
    "data": "0x41cd5add4fd13aedd64521e363ea279923575ff39718065d38bd46f0e6632e8e"
  }
]
```

#### Returns

- `Data` - Result of the call

#### Example

Request
```bash
curl --data '{"method":"private_call","params":["0x2",{"from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1","to":"0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b","data":"0x41cd5add4fd13aedd64521e363ea279923575ff39718065d38bd46f0e6632e8e"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### private_sendTransaction

Make an onchain call to the private contract in order to modify its state.

#### Parameters

0. `Data` - Signed regular transaction (in hex)

```js
params: ["0xf8840180830e57e094cd96d189596c690ff983e4e14d1838d0305186dc80a4bc64b76d2a0000000000000000000000000000000000000000000000000000000000000045a00b3aa43f869f087a3aec97ba4232b15f9f0ab964c17953ca556b944e424d1f84a058d6edf93ad3c2bb0f0513e9ef391bbe8f7400668aaa51344ee4bf1a587e0218"]
```

#### Returns

- [object Object]

#### Example

Request
```bash
curl --data '{"method":"private_sendTransaction","params":["0xf8840180830e57e094cd96d189596c690ff983e4e14d1838d0305186dc80a4bc64b76d2a0000000000000000000000000000000000000000000000000000000000000045a00b3aa43f869f087a3aec97ba4232b15f9f0ab964c17953ca556b944e424d1f84a058d6edf93ad3c2bb0f0513e9ef391bbe8f7400668aaa51344ee4bf1a587e0218"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "transactionHash": "0x63c715e88f7291e66069302f6fcbb4f28a19ef5d7cbd1832d0c01e221c0061c6",
    "contractAddress": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
    "status": "0x0" // 0
  }
}
```

