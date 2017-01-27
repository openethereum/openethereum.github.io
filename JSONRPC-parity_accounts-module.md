# The `parity_accounts` Module

## JSON-RPC methods

- [parity_allAccountsInfo](#parity_allaccountsinfo)
- [parity_changePassword](#parity_changepassword)
- [parity_getDappsAddresses](#parity_getdappsaddresses)
- [parity_getNewDappsWhitelist](#parity_getnewdappswhitelist)
- [parity_importGethAccounts](#parity_importgethaccounts)
- [parity_killAccount](#parity_killaccount)
- [parity_listGethAccounts](#parity_listgethaccounts)
- [parity_listRecentDapps](#parity_listrecentdapps)
- [parity_newAccountFromPhrase](#parity_newaccountfromphrase)
- [parity_newAccountFromSecret](#parity_newaccountfromsecret)
- [parity_newAccountFromWallet](#parity_newaccountfromwallet)
- [parity_removeAddress](#parity_removeaddress)
- [parity_setAccountMeta](#parity_setaccountmeta)
- [parity_setAccountName](#parity_setaccountname)
- [parity_setDappsAddresses](#parity_setdappsaddresses)
- [parity_setNewDappsWhitelist](#parity_setnewdappswhitelist)
- [parity_testPassword](#parity_testpassword)

## JSON-RPC API Reference

### parity_allAccountsInfo

returns a map of accounts as an object.

#### Parameters

None

#### Returns

- `Array` - Account metadata.
    - `name`: `String` - Account name.
    - `meta`: `String` - Encoded JSON string the defines additional account metadata.
    - `uuid`: `String` - The account Uuid, or `null` if not available/unknown/not applicable.

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

### parity_getDappsAddresses

Returns the list of accounts available to a specific dapp.

#### Parameters

0. `String` - Dapp Id.

```js
params: ["web"]
```

#### Returns

- `Array` - The list of available accounts.

#### Example

Request
```bash
curl --data '{"method":"parity_getDappsAddresses","params":["web"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_getNewDappsWhitelist

Returns the list of accounts available to a new dapps.

#### Parameters

None

#### Returns

- `Array` - The list of available accounts, can be `null`.

#### Example

Request
```bash
curl --data '{"method":"parity_getNewDappsWhitelist","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_importGethAccounts

Imports a list of accounts from Geth.

#### Parameters

0. `Array` - List of the Geth addresses to import.

#### Returns

- `Array` - Array of the imported addresses.

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

***

### parity_listRecentDapps

Returns a list of the most recent active dapps.

#### Parameters

None

#### Returns

- `Array` - Array of Dapp Ids.

#### Example

Request
```bash
curl --data '{"method":"parity_listRecentDapps","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": ["web"]
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

0. `String` - JSON
0. `String` - Password

#### Returns

- `Address` - The created address

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

### parity_setDappsAddresses

Sets the available addresses for a dapp.

#### Parameters

0. `String` - Dapp Id.
0. `Array` - Array of available accounts available to the dapp.

```js
params: [
  "web",
  ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
]
```

#### Returns

- `Boolean` - `true` if the call was successful.

#### Example

Request
```bash
curl --data '{"method":"parity_setDappsAddresses","params":["web",["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

### parity_setNewDappsWhitelist

Sets the list of accounts available to new dapps.

#### Parameters

0. `Array` - List of accounts available by default.

```js
params: [
  ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
]
```

#### Returns

- `Boolean` - `true` if the call was successful

#### Example

Request
```bash
curl --data '{"method":"parity_setNewDappsWhitelist","params":[["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
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

