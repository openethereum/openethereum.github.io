---
title: The `signer` Module
---

## JSON-RPC methods

- [signer_confirmRequest](#signer_confirmrequest)
- [signer_confirmRequestRaw](#signer_confirmrequestraw)
- [signer_confirmRequestWithToken](#signer_confirmrequestwithtoken)
- [signer_generateAuthorizationToken](#signer_generateauthorizationtoken)
- [signer_generateWebProxyAccessToken](#signer_generatewebproxyaccesstoken)
- [signer_rejectRequest](#signer_rejectrequest)
- [signer_requestsToConfirm](#signer_requeststoconfirm)
- [signer_subscribePending](#signer_subscribepending)
- [signer_unsubscribePending](#signer_unsubscribepending)

## JSON-RPC API Reference

### signer_confirmRequest

Confirm a request in the signer queue

#### Parameters

0. `Quantity` - The request id.
0. `Object` - Modify the transaction before confirmation.
    - `gasPrice`:   `Quantity` - (optional) Modify the gas price provided by the sender in Wei.
    - `gas`:   `Quantity` - (optional) Gas provided by the sender in Wei.
    - `condition`:   `Object` - (optional) Condition for scheduled transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ timestamp: 1491290692 }`.
0. `String` - The account password

```js
params: [
  "0x1", // 1
  {},
  "hunter2"
]
```

#### Returns

- `Object` - The status of the confirmation, depending on the request type.

#### Example

Request
```bash
curl --data '{"method":"signer_confirmRequest","params":["0x1",{},"hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {}
}
```

***

### signer_confirmRequestRaw

Confirm a request in the signer queue providing signed request.

#### Parameters

0. `Quantity` - Integer - The request id
0. `Data` - Signed request (RLP encoded transaction)

```js
params: [
  "0x1", // 1
  "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
]
```

#### Returns

- `Object` - The status of the confirmation, depending on the request type.

#### Example

Request
```bash
curl --data '{"method":"signer_confirmRequestRaw","params":["0x1","0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {}
}
```

***

### signer_confirmRequestWithToken

Confirm specific request with rolling token.

#### Parameters

0. `Quantity` - The request id.
0. `Object` - Modify the transaction before confirmation.
    - `gasPrice`:   `Quantity` - (optional) Modify the gas price provided by the sender in Wei.
    - `gas`:   `Quantity` - (optional) Gas provided by the sender in Wei.
    - `condition`:   `Object` - (optional) Conditional submission of the transaction. Can be either an integer block number `{ block: 1 }` or UTC timestamp (in seconds) `{ time: 1491290692 }` or `null`.
0. `String` - Password (initially) or a token returned by the previous call.

```js
params: [
  "0x1", // 1
  {},
  "hunter2"
]
```

#### Returns

- `Object` - Status.
    - `result`:   `Object` - The status of the confirmation, depending on the request type.
    - `token`:   `String` - Token used to authenticate the next request.

#### Example

Request
```bash
curl --data '{"method":"signer_confirmRequestWithToken","params":["0x1",{},"hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "result": { ... },
    "token": "cAF2w5LE7XUZ3v3N"
  }
}
```

***

### signer_generateAuthorizationToken

Generates a new authorization token.

#### Parameters

None

#### Returns

- `String` - The new authorization token.

#### Example

Request
```bash
curl --data '{"method":"signer_generateAuthorizationToken","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "bNGY-iIPB-j7zK-RSYZ"
}
```

***

### signer_generateWebProxyAccessToken

Generates a new web proxy access token.

#### Parameters

0. `String` - Domain for which the token is valid. Only requests to this domain will be allowed.

```js
params: ["https://parity.io"]
```

#### Returns

- `String` - The new web proxy access token.

#### Example

Request
```bash
curl --data '{"method":"signer_generateWebProxyAccessToken","params":["https://parity.io"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "MOWm0tEJjwthDiTU"
}
```

***

### signer_rejectRequest

Rejects a request in the signer queue

#### Parameters

0. `Quantity` - Integer - The request id

```js
params: [
  "0x1" // 1
]
```

#### Returns

- `Boolean` - The status of the rejection

#### Example

Request
```bash
curl --data '{"method":"signer_rejectRequest","params":["0x1"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### signer_requestsToConfirm

Returns a list of the transactions awaiting authorization.

#### Parameters

None

#### Returns

- `Array` - A list of the outstanding transactions.

#### Example

Request
```bash
curl --data '{"method":"signer_requestsToConfirm","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [ ... ]
}
```

***

### signer_subscribePending


    Starts a subscription for transactions in the confirmation queue.
    Each event contains all transactions currently in the queue.

    An example notification received by subscribing to this event:
    ```
    {"jsonrpc":"2.0","method":"signer_pending","params":{"subscription":"0x416d77337e24399d","result":[]}}
    ```

    You can unsubscribe using `signer_unsubscribePending` RPC method. Subscriptions are also tied to a transport
    connection, disconnecting causes all subscriptions to be canceled.
    

#### Parameters

None

#### Returns

- `String` - Assigned subscription ID

#### Example

Request
```bash
wscat -c localhost:8546
>{"method":"signer_subscribePending","params":[],"id":1,"jsonrpc":"2.0"}
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x416d77337e24399d"
}
```

***

### signer_unsubscribePending

Unsubscribes from pending transactions subscription.

#### Parameters

0. `String` - Subscription ID

```js
params: ["0x416d77337e24399d"]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
wscat -c localhost:8546
>{"method":"signer_unsubscribePending","params":["0x416d77337e24399d"],"id":1,"jsonrpc":"2.0"}
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

