# The `signer` Module

## JSON-RPC methods

- [signer_confirmRequest](#signer_confirmrequest)
- [signer_confirmRequestRaw](#signer_confirmrequestraw)
- [signer_confirmRequestWithToken](#signer_confirmrequestwithtoken)
- [signer_generateAuthorizationToken](#signer_generateauthorizationtoken)
- [signer_generateWebProxyAccessToken](#signer_generatewebproxyaccesstoken)
- [signer_rejectRequest](#signer_rejectrequest)
- [signer_requestsToConfirm](#signer_requeststoconfirm)

## JSON-RPC API Reference

### signer_confirmRequest

Confirm a request in the signer queue

#### Parameters

0. `Quantity` - The request id.
0. `Object` - Modify the transaction before confirmation.
    - `gasPrice`: `Quantity` - (optional) Modify the gas price provided by the sender in Wei.
    - `gas`: `Quantity` - (optional) Gas provided by the sender in Wei.
    - `minBlock`: `Quantity` | `Tag` - (optional) Integer block number, or the string `'latest'`, `'earliest'` or `'pending'`. Request will not be propagated till the given block is reached.
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

Confirm specific request with token.

#### Parameters

0. `Quantity` - The request id.
0. `Object` - Modify the transaction before confirmation.
    - `gasPrice`: `Quantity` - (optional) Modify the gas price provided by the sender in Wei.
    - `gas`: `Quantity` - (optional) Gas provided by the sender in Wei.
    - `minBlock`: `Quantity` | `Tag` - (optional) Integer block number, or the string `'latest'`, `'earliest'` or `'pending'`. Request will not be propagated till the given block is reached.
0. `String` - Password.

```js
params: [
  "0x1", // 1
  {},
  "hunter2"
]
```

#### Returns

- `Object` - Status.
    - `result`: `Object` - The status of the confirmation, depending on the request type.
    - `token`: `String` - Token used to authenticate the request.

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

None

#### Returns

- `String` - The new web proxy access token.

#### Example

Request
```bash
curl --data '{"method":"signer_generateWebProxyAccessToken","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

