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
TODO


#### personal_newAccount
Creates new account (it becomes the new current unlocked account)

##### Parameters
1. `STR` - Password for the new account

##### Returns
TODO


#### personal_newAccountFromPhrase
Creates new account from the given phrase using standard brainwallet mechanism.

##### Parameters
1. `STR` - Phrase used to generate the key for the brainwallet account
2. `STR` - Password to use to lock the new account

##### Returns
TODO


#### personal_newAccountFromWallet
Creates new account from the given JSON wallet

##### Parameters
1. `STR` - TODO
2. `STR` - Password to use to lock the new account

##### Returns
TODO


#### personal_unlockAccount
Unlocks specified account for use (there can only be one unlocked account at any one time)

If permanent unlocking is disabled (the default) then the duration argument will be ignored, and the account will be unlocked for a single signing.
With permanent locking enabled, the duration sets the number of seconds to hold the account open for.  It will default to 300 seconds.  Passing 0 unlocks the account indefinitely.

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - Password to use to unlock the account
3. `QUANTITY` - (optional)

##### Returns
TODO


#### personal_signAndSendTransaction
Sends transaction and signs it in a single call.  The account is not unlocked in such a case.

##### Parameters
1. `TransactionRequest` - TODO
2. `STR` - Password to use to unlock the account

##### Returns
TODO


#### personal_setAccountName
Set an account's name

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - The new account name

##### Returns
TODO


#### personal_setAccountMeta
Set an account's metadata string

##### Parameters
1. `DATA`, 20 bytes - Account identifier
2. `STR` - Replacement description to link to the account

##### Returns
TODO


#### personal_accountsInfo
Returns account's information

##### Returns
TODO


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