# The `parity` Module

These are RPCs specific to the operation of Parity.

### Block authoring (aka "mining")
- `parity_extraData` `()` *returns* `d: DATA` Get the current extra data used `d`.
- `parity_defaultExtraData` `()` *returns* `d: DATA` Get the default extra data `d`.
- `parity_gasFloorTarget` `()` *returns* `t: QUANTITY` Get the gas-floor target `t`.
- `parity_gasCeilTarget` `()` *returns* `t: QUANTITY` Get the gas-ceiling target `t`.
- `parity_minGasPrice` `()` *returns* `p: QUANTITY` Get the minimal allowable gas price in Wei `p`.
- `parity_transactionsLimit` `()` *returns* `l: QUANTITY` Get the transaction queue size limit `l`.

### Development
- `parity_devLogs` `()` *returns* `[s0: STR, s1: STR, ...]` Get the array of recent logs `s0`, `s1`, ....
- `parity_devLogsLevels` `()` *returns* `l: STR` Get configured logger settings.

### Node Settings
- `parity_signerPort` `()` *returns* `INT` ???
- `parity_dappsPort` `()` *returns* `INT` ???
- `parity_dappsInterface` `()` *returns* `STR` ???
- `parity_mode` `()` *returns* `STR` ???
- `parity_enode` `()` *returns* `STR` ???

### Network Information
- `parity_netChain` `()` *returns* `s: STR` Get the chain on which the node is operating `s`.
- `parity_netPeers` `()` *returns* `{active: QUANTITY, connected: QUANTITY, max: QUANTITY, peers: [PeerDetails, ..]` Get peers details
- `parity_netPort` `()` *returns* `i: INT` Get the port on which we are listening `i`.
- `parity_nodeName` `()` *returns* `s: STR` Get the node's identifying name `s`.
- `parity_rpcSettings` `()` *returns* `` ???
- `parity_gasPriceHistogram` `()` *returns* `` ???
- `parity_unsignedTransactionsCount`  `()` *returns* `c: INT` Get number of requests awaiting confirmation in Trusted Signer
- `parity_registryAddress` `()` *returns* `` ???
- `parity_pendingTransactions` `()` *returns* `` ???
- `parity_nextNonce` `()` *returns* `QUANTITY` ???

### Accounts (read-only) and Signatures
- `parity_accounts` `()` *returns* `` ???
- `parity_listAccounts` `()` *returns* `` ???
- `parity_phraseToAddress` `(STR)` *returns* `s: STR` Returns whatever address would be derived from the given phrase if it were to seed a brainwallet.
- `parity_generateSecretPhrase` `()` *returns* `s: STR` get a cryptographically secure secret phrase with around 160 bits of entropy.
- `parity_postSign` `(ADDRESS, HASH)` *returns* `` ???
- `parity_postTransaction` `(TRANSACTION)` *returns* `` ???
- `parity_checkRequest` `(QUANTITY)` *returns* `` ???
- `parity_encryptMessage` `(PUBKEY, DATA)` *returns* `` ???
- `parity_decryptMessage` `(ADDRESS, DATA)` *returns* `` ???

# The `parity_accounts` Module

## JSON-RPC methods

* [parity_listAccounts](#parity_listAccounts)
* [parity_listStorageKeys](#parity_listStorageKeys)
* [parity_newAccount](#parity_newaccount)
* [parity_newAccountFromPhrase](#parity_newaccountfromphrase)
* [parity_newAccountFromWallet](#parity_newaccountfromwallet)
* [parity_setAccountName](#parity_setaccountname)
* [parity_setAccountMeta](#parity_setaccountmeta)
* [parity_accountsInfo](#parity_accountsinfo)

## JSON RPC API Reference

#### parity_listAccounts
Lists all stored accounts. Requires a FatDB database. 

##### Parameters
1. `QUANTITY` - Number of accounts to return.
2. `DATA` - Starting account address. List starts from the account immediately following this account. This should be set to `null` to start from the beginning or to the last item from a previous batch to continue enumeration.
3. `QUANTITY|TAG` - Integer block number, or the string "latest", "earliest" or "pending", see the [default block parameter](https://github.com/ethereum/wiki/wiki/JSON-RPC#the-default-block-parameter)

##### Returns
`Array` of `DATA`, 20 bytes - A list of account identifiers. The order of the returned values is unspecified.
##### Example
```js
//Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"parity_listAccounts","params":[5, null],"id":83}'

// Result
{
    "jsonrpc": "2.0",
    "id": 83,
    "result": [
        "0x1067dcb77e3390563819a94907adb53a402dd707",
        "0x843fd22c88d59e57ae1856a871a5d95e95b0a656",
        "0x2d29454cfa2f3dfa06a6ffd19eb9ef87281005f7",
        "0x30903bb9a2e424e120176a165331ab132601560b",
        "0xdcd0b6fa4f0a26a7b12325b0d09b5b809c5aef84"
    ],
}
```

#### parity_listStorageKeys
Lists all account storage keys. Requires a FatDB database. 

##### Parameters
1. `DATA` - Account address.
2. `QUANTITY` - Number of storage keys to return.
3. `DATA` - Starting storage key. List starts from the key immediately following this key. This should be set to `null` to start from the beginning or to the last item from a previous batch to continue enumeration.
4. `QUANTITY|TAG` - Integer block number, or the string "latest", "earliest" or "pending", see the [default block parameter](https://github.com/ethereum/wiki/wiki/JSON-RPC#the-default-block-parameter)

##### Returns
`Array` of `DATA`, 20 bytes - A list of storage keys. The order of the returned values is unspecified.

##### Example
```js
//Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"parity_listStorageKeys","params":["0xBB9bc244D798123fDe783fCc1C72d3Bb8C189413", 5, null],"id":83}'

// Result
{
    "jsonrpc": "2.0",
    "id": 83,
    "result": [
        "0xdb15094c8bbcc75cca5b8a449f2d2773a1f99820ab21a8b7d61a05aa112d43c4",
        "0x15166c0c18a3e9ac633d6fe46b5e527c99a1eb3c84d710210c5cd839c009fe8d",
        "0x661ee31c1965b02ee935c0f2f7d78daaa9f14c2dc0382362666a0c9b64659e7e",
        "0x9dcd180d9f2fdb4f500fc6ff8edb20ccb533ad0235cb9d3c6b07403f1ff5d30c",
        "0xcc514b57213d8bf433c5b550a92061eae5637d287ab751a52974575d70c25663"
    ],
}
```

#### parity_newAccount
Creates new account (it becomes the new current unlocked account)

##### Parameters
1. `STR` - Password for the new account

##### Returns

`DATA`, 20 bytes - The identifier of the new account

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"parity_newAccount","params":["hunter2"],"id":22}' localhost:8545

// Result
{
    "jsonrpc": "2.0",
    "id": 22,
    "result": "0x8f0227d45853a50eefd48dd4fec25d5b3fd2295e"
}
```


#### parity_newAccountFromPhrase
Creates new account from the given phrase using standard brainwallet mechanism.

##### Parameters
1. `STR` - Phrase used to generate the key for the brainwallet account
2. `STR` - Password to use to lock the new account

##### Returns
`DATA`, 20 bytes - The identifier of the new account

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"parity_newAccountFromPhrase","params":["insecure","hunter2"],"id": 22}' localhost:8545

// Result
{
    "jsonrpc": "2.0",
    "id":22,
    "result": "0xfc390d8a8ddb591b010fda52f4db4945742c3809"
}
```


#### parity_newAccountFromWallet
Creates new account from the given JSON wallet

##### Parameters
1. `STR` - TODO
2. `STR` - Password to use to lock the new account

##### Returns
`DATA`, 20 bytes - The identifier of the new account


#### parity_setAccountName
Set an account's name

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - The new account name

##### Returns
Always returns `null`.

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"parity_setAccountName","params":["0xfc390d8a8ddb591b010fda52f4db4945742c3809","Savings"],"id": 22}' localhost:8545

// Response
{
    "jsonrpc": "2.0",
    "id":22,
    "result":null
}
```


#### parity_setAccountMeta
Set an account's metadata string

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - Replacement application specific metadata to link to the account

##### Returns
TODO


#### parity_accountsInfo
Returns account's information

##### Returns
TODO

##### Example
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"parity_accountsInfo","id": 22}' localhost:8545

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
