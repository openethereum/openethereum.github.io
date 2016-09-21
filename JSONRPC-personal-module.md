# The `personal` Module

## JSON-RPC methods

* [personal_signerEnabled](#personal_signerEnabled)
* [personal_listAccounts](#personal_listAccounts)
* [personal_newAccount](#personal_newAccount)
* [personal_newAccountFromPhrase](#personal_newAccountFromPhrase)
* [personal_newAccountFromWallet](#personal_newAccountFromWallet)
* [personal_unlockAccount](#personal_unlockAccount)
* [personal_signAndSendTransaction](#personal_signAndSendTransaction)
* [personal_setAccountName](#personal_setAccountName)
* [personal_setAccountMeta](#personal_setAccountMeta)
* [personal_accountsInfo](#personal_accountsInfo)
* [personal_requestsToConfirm](#personal_requestsToConfirm)
* [personal_confirmRequest](#personal_confirmRequest)
* [personal_rejectRequest](#personal_rejectRequest)

## JSON RPC API Reference

#### personal_signerEnabled
Returns `true` if  Trusted Signer is enabled, `false` otherwise.

#### personal_listAccounts
Lists all stored accounts

##### Returns
`Array` of `DATA`, 20 bytes - A list of account identifiers.

##### Example
```js
//Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"personal_listAccounts","params":[],"id":83}'

// Result
{
    "jsonrpc": "2.0",
    "id": 83,
    "result": [
        "0x7bf87721a96849d168de02fd6ea5986a3a147383",
        "0xca807a90fd64deed760fb98bf0869b475c469348"
    ],
}
```


#### personal_newAccount
Creates new account (it becomes the new current unlocked account)

##### Parameters
1. `STR` - Password for the new account

##### Returns

`DATA`, 20 bytes - The identifier of the new account

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"personal_newAccount","params":["hunter2"],"id":22}' localhost:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 22,
    "result": "0x8f0227d45853a50eefd48dd4fec25d5b3fd2295e"
}
```


#### personal_newAccountFromPhrase
Creates new account from the given phrase using standard brainwallet mechanism.

##### Parameters
1. `STR` - Phrase used to generate the key for the brainwallet account
2. `STR` - Password to use to lock the new account

##### Returns
`DATA`, 20 bytes - The identifier of the new account

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"personal_newAccountFromPhrase","params":["insecure","hunter2"],"id": 22}' localhost:8545

// Result
{
    "jsonrpc": "2.0",
    "id":22,
    "result": "0xfc390d8a8ddb591b010fda52f4db4945742c3809"
}
```


#### personal_newAccountFromWallet
Creates new account from the given JSON wallet

##### Parameters
1. `STR` - TODO
2. `STR` - Password to use to lock the new account

##### Returns
`DATA`, 20 bytes - The identifier of the new account


#### personal_unlockAccount
Unlocks specified account for use (there can only be one unlocked account at any one time)

If permanent unlocking is disabled (the default) then the duration argument will be ignored, and the account will be unlocked for a single signing.
With permanent locking enabled, the duration sets the number of seconds to hold the account open for.  It will default to 300 seconds.  Passing 0 unlocks the account indefinitely.

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - Password to use to unlock the account
3. `QUANTITY`- (can be null)

##### Returns
True if the account exists and could be unlocked using the password, false otherwise.

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"personal_unlockAccount","params":["0xfc390d8a8ddb591b010fda52f4db4945742c3809","hunter2",null],"id": 22}' localhost:8545

// Result
{
    "jsonrpc":"2.0",
    "id":22,
    "result":true
}
```


#### personal_signAndSendTransaction
Sends transaction and signs it in a single call.  The account does not need to be unlocked to make this call, and will not be left unlocked after.

##### Parameters

1. `Object` - The transaction object
  - `from`: `DATA`, 20 Bytes - The address of the account to unlock and send the transaction from.
  - `to`: `DATA`, 20 Bytes - (optional when creating new contract) The address the transaction is directed to.
  - `gas`: `QUANTITY`  - (optional, default: 90000) Integer of the gas provided for the transaction execution. It will return unused gas.
  - `gasPrice`: `QUANTITY`  - (optional, default: To-Be-Determined) Integer of the gasPrice used for each paid gas
  - `value`: `QUANTITY`  - (optional) Integer of the value send with this transaction
  - `data`: `DATA`  - The compiled code of a contract OR the hash of the invoked method signature and encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI)
  - `nonce`: `QUANTITY`  - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.

2. `STR` - Password to use to temporarily unlock the account

##### Returns
`DATA`, 32 Bytes - The hash of the new transaction.


#### personal_setAccountName
Set an account's name

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - The new account name

##### Returns
Always returns `null`.

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"personal_setAccountName","params":["0xfc390d8a8ddb591b010fda52f4db4945742c3809","Savings"],"id": 22}' localhost:8545

// Response
{
    "jsonrpc": "2.0",
    "id":22,
    "result":null
}
```


#### personal_setAccountMeta
Set an account's metadata string

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - Replacement application specific metadata to link to the account

##### Returns
TODO


#### personal_accountsInfo
Returns account's information

##### Returns
TODO

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"personal_accountsInfo","id": 22}' localhost:8545

// Result
{
    "jsonrpc": "2.0",
    "id":22,
    "result":{
        "0xfc390d8a8ddb591b010fda52f4db4945742c3809":{
            "name":"Savings",
            "uuid":"7fee0393-7571-2b4f-8672-862fea01a4a0",
            "meta":"{}"
        }
    }
}
```


#### personal_requestsToConfirm
Returns a list of items to confirm.

##### Returns
TODO


#### personal_confirmRequest
Confirm specific request

##### Parameters
1. `DATA`, 32 Bytes - A request id (TODO)
2. `TransactionModification` - TODO
3. `STR` - Password to use to unlock the account

##### Returns
TODO

#### personal_rejectRequest
Reject a confirmation request

##### Parameters
1. `DATA`, 32 Bytes - A request id (TODO)

##### Returns
TODO