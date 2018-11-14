---
title: The `parity_accounts` Module
---

## JSON-RPC methods

- [parity_allAccountsInfo](#parity_allaccountsinfo)
- [parity_changePassword](#parity_changepassword)
- [parity_deriveAddressHash](#parity_deriveaddresshash)
- [parity_deriveAddressIndex](#parity_deriveaddressindex)
- [parity_exportAccount](#parity_exportaccount)
- [parity_importGethAccounts](#parity_importgethaccounts)
- [parity_killAccount](#parity_killaccount)
- [parity_listGethAccounts](#parity_listgethaccounts)
- [parity_newAccountFromPhrase](#parity_newaccountfromphrase)
- [parity_newAccountFromSecret](#parity_newaccountfromsecret)
- [parity_newAccountFromWallet](#parity_newaccountfromwallet)
- [parity_removeAddress](#parity_removeaddress)
- [parity_setAccountMeta](#parity_setaccountmeta)
- [parity_setAccountName](#parity_setaccountname)
- [parity_testPassword](#parity_testpassword)

## JSON-RPC API Reference

### parity_allAccountsInfo

returns a map of accounts as an object.

#### Parameters

None

#### Returns

- `Array` - Account metadata.
    - `name`:   `String` - Account name.
    - `meta`:   `String` - Encoded JSON string the defines additional account metadata.
    - `uuid`:   `String` - The account Uuid, or `null` if not available/unknown/not applicable.

#### Example

Request
```bash
curl --data '{"method":"parity_allAccountsInfo","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "0x00a289b43e1e4825dbedf2a78ba60a640634dc40": {
      "meta": "{}",
      "name": "Foobar",
      "uuid": "0b9e70e6-235b-682d-a15c-2a98c71b3945"
    }
  }
}
```

***

### parity_changePassword

Change the password for a given account.

#### Parameters

0. `Address` - Address of the account.
0. `String` - Old password.
0. `String` - New password.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "hunter2",
  "bazqux5"
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_changePassword","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2","bazqux5"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_deriveAddressHash

Derive new address from given account address using specific hash.

#### Parameters

0. `Address` - Account address to derive from.
0. `String` - Password to the account.
0. `Object` - Derivation hash and type (`soft` or `hard`). E.g. `{ hash: "0x123..123", type: "hard" }`.
0. `Boolean` - Flag indicating if the account should be saved.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "hunter2",
  {
    "hash": "0x2547ea3382099c7c76d33dd468063b32d41016aacb02cbd51ebc14ff5d2b6a43",
    "type": "hard"
  },
  false
]
```

#### Returns

- `Address` - 20 Bytes new derived address.

#### Example

Request
```bash
curl --data '{"method":"parity_deriveAddressHash","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2",{"hash":"0x2547ea3382099c7c76d33dd468063b32d41016aacb02cbd51ebc14ff5d2b6a43","type":"hard"},false],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_deriveAddressIndex

Derive new address from given account address using hierarchical derivation (sequence of 32-bit integer indices).

#### Parameters

0. `Address` - Account address to export.
0. `String` - Password to the account.
0. `Array` - Hierarchical derivation sequence of index and type (`soft` or `hard`). E.g. `[{index:1,type:"hard"},{index:2,type:"soft"}]`.
0. `Boolean` - Flag indicating if the account should be saved.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "hunter2",
  [
    {
      "index": 1,
      "type": "hard"
    },
    {
      "index": 2,
      "type": "soft"
    }
  ],
  false
]
```

#### Returns

- `Address` - 20 Bytes new derived address.

#### Example

Request
```bash
curl --data '{"method":"parity_deriveAddressIndex","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2",[{"index":1,"type":"hard"},{"index":2,"type":"soft"}],false],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_exportAccount

Returns a standard wallet file for given account if password matches.

#### Parameters

0. `Address` - Account address to export.
0. `String` - Password to the account.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "hunter2"
]
```

#### Returns

- `Object` - Standard wallet JSON.

#### Example

Request
```bash
curl --data '{"method":"parity_exportAccount","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "address": "0042e5d2a662eeaca8a7e828c174f98f35d8925b",
    "crypto": {
      "cipher": "aes-128-ctr",
      "cipherparams": {
        "iv": "a1c6ff99070f8032ca1c4e8add006373"
      },
      "ciphertext": "df27e3db64aa18d984b6439443f73660643c2d119a6f0fa2fa9a6456fc802d75",
      "kdf": "pbkdf2",
      "kdfparams": {
        "c": 10240,
        "dklen": 32,
        "prf": "hmac-sha256",
        "salt": "ddc325335cda5567a1719313e73b4842511f3e4a837c9658eeb78e51ebe8c815"
      },
      "mac": "3dc888ae79cbb226ff9c455669f6cf2d79be72120f2298f6cb0d444fddc0aa3d"
    },
    "id": "6a186c80-7797-cff2-bc2e-7c1d6a6cc76e",
    "meta": "{\"passwordHint\":\"parity-export-test\",\"timestamp\":1490017814987}",
    "name": "parity-export-test",
    "version": 3
  }
}
```

***

### parity_importGethAccounts

Imports a list of accounts from Geth.

#### Parameters

0. `Array` - List of the Geth addresses to import.

```js
params: [
  ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
]
```

#### Returns

- `Array` - Array of the imported addresses.

#### Example

Request
```bash
curl --data '{"method":"parity_importGethAccounts","params":[["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_killAccount

Deletes an account.

#### Parameters

0. `Address` - The account to remove.
0. `String` - Account password.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "hunter2"
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_killAccount","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_listGethAccounts

Returns a list of the accounts available from Geth.

#### Parameters

None

#### Returns

- `Array` - 20 Bytes addresses owned by the client.

#### Example

Request
```bash
curl --data '{"method":"parity_listGethAccounts","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_newAccountFromPhrase

Creates a new account from a recovery phrase.

#### Parameters

0. `String` - Recovery phrase.
0. `String` - Password.

```js
params: [
  "stylus outing overhand dime radial seducing harmless uselessly evasive tastiness eradicate imperfect",
  "hunter2"
]
```

#### Returns

- `Address` - The created address.

#### Example

Request
```bash
curl --data '{"method":"parity_newAccountFromPhrase","params":["stylus outing overhand dime radial seducing harmless uselessly evasive tastiness eradicate imperfect","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_newAccountFromSecret

Creates a new account from a private ethstore secret key.

#### Parameters

0. `Data` - Secret, 32-byte hex
0. `String` - Password

```js
params: [
  "0x1db2c0cf57505d0f4a3d589414f0a0025ca97421d2cd596a9486bc7e2cd2bf8b",
  "hunter2"
]
```

#### Returns

- `Address` - The created address.

#### Example

Request
```bash
curl --data '{"method":"parity_newAccountFromSecret","params":["0x1db2c0cf57505d0f4a3d589414f0a0025ca97421d2cd596a9486bc7e2cd2bf8b","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_newAccountFromWallet

Creates a new account from a JSON import

#### Parameters

0. `String` - Wallet JSON encoded to a string.
0. `String` - Password.

```js
params: [
  "{\"id\": \"9c62e86b-3cf9...\", ...}",
  "hunter2"
]
```

#### Returns

- `Address` - The created address

#### Example

Request
```bash
curl --data '{"method":"parity_newAccountFromWallet","params":["{\"id\": \"9c62e86b-3cf9...\", ...}","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_removeAddress

Removes an address from the addressbook.

#### Parameters

0. `Address` - The address to remove.

```js
params: ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
```

#### Returns

- `Boolean` - `true`if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_removeAddress","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setAccountMeta

Sets metadata for the account

#### Parameters

0. `Address` - Address
0. `String` - Metadata (JSON encoded)

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "{\"foo\":\"bar\"}"
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_setAccountMeta","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","{\"foo\":\"bar\"}"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setAccountName

Sets a name for the account

#### Parameters

0. `Address` - Address
0. `String` - Name

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "Foobar"
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_setAccountName","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","Foobar"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_testPassword

Checks if a given password can unlock a given account, without actually unlocking it.

#### Parameters

0. `Address` - Account to test.
0. `String` - Password to test.

```js
params: [
  "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "hunter2"
]
```

#### Returns

- `Boolean` - `true` if the account and password are valid.

#### Example

Request
```bash
curl --data '{"method":"parity_testPassword","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

