# The `signer` Module

## JSON RPC methods

- [signer_confirmRequest](#signer_confirmrequest)
- [signer_confirmRequestRaw](#signer_confirmrequestraw)
- [signer_generateAuthorizationToken](#signer_generateauthorizationtoken)
- [signer_generateWebProxyAccessToken](#signer_generatewebproxyaccesstoken)
- [signer_rejectRequest](#signer_rejectrequest)
- [signer_requestsToConfirm](#signer_requeststoconfirm)
- [signer_signerEnabled](#signer_signerenabled)

## JSON RPC API Reference

***

### signer_confirmRequest

Confirm a request in the signer queue

#### parameters

- `Quantity` - The request id.
- `Object` - Modify the transaction before confirmation.
    - `gas`/`Quantity` - (optional) Gas provided by the sender in Wei.
    - `gasPrice`/`Quantity` - (optional) Modify the gas price provided by the sender in Wei.
    - `minBlock`/`BlockNumber` - (optional) Integer block number, or the string `'latest'`, `'earliest'` or `'pending'`. Request will not be propagated till the given block is reached.
- `String` - The account password

#### returns

- `Boolean` - The status of the confirmation

#### example

```bash
# Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"signer_confirmRequest","params":["0x1",{},"hunter2"],"id":1}' localhost:8545

# Response
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

### signer_confirmRequestRaw

Confirm a request in the signer queue providing signed request.

#### parameters

- `Quantity` - Integer - The request id
- `Data` - Signed request (RLP encoded transaction)

#### returns

- `Boolean` - The status of the confirmation

#### example

```bash
# Response
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

### signer_generateAuthorizationToken

Generates a new authorization token.

#### parameters

none

#### returns

- `String` - The new authorization token.

#### example

```bash
# Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"signer_generateAuthorizationToken","params":[],"id":1}' localhost:8545

# Response
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "bNGY-iIPB-j7zK-RSYZ"
}
```

***

### signer_generateWebProxyAccessToken

Generates a new web proxy access token.

#### parameters

none

#### returns

- `String` - The new web proxy access token.

#### example

```bash
# Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"signer_generateWebProxyAccessToken","params":[],"id":1}' localhost:8545

# Response
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "MOWm0tEJjwthDiTU"
}
```

***

### signer_rejectRequest

Rejects a request in the signer queue

#### parameters

- `Quantity` - Integer - The request id

#### returns

- `Boolean` - The status of the rejection

#### example

```bash
# Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"signer_rejectRequest","params":["0x1"],"id":1}' localhost:8545

# Response
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

### signer_requestsToConfirm

Returns a list of the transactions awaiting authorization.

#### parameters

none

#### returns

- `Array` - A list of the outstanding transactions.

#### example

```bash
# Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"signer_requestsToConfirm","params":[],"id":1}' localhost:8545

# Response
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": []
}
```

***

### signer_signerEnabled

Returns whether signer is enabled/disabled.

#### parameters

none

#### returns

- `Boolean` - `true` when enabled, `false` when disabled.

#### example

```bash
# Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"signer_signerEnabled","params":[],"id":1}' localhost:8545

# Response
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```
