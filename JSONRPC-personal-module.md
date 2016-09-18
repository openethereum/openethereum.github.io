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

#### personal_newAccount
Creates new account (it becomes the new current unlocked account)

#### personal_newAccountFromPhrase
Creates new account from the given phrase using standard brainwallet mechanism.

#### personal_newAccountFromWallet
Creates new account from the given JSON wallet

#### personal_unlockAccount
Unlocks specified account for use (there can only be one unlocked account at any one time)

#### personal_signAndSendTransaction
Sends transaction and signs it in a single call.  The account is not unlocked in such a case.

#### personal_setAccountName
Set an account's name

#### personal_setAccountMeta
Set an account's metadata string

#### personal_accountsInfo
Returns account's information

#### personal_requestsToConfirm
Returns a list of items to confirm.

#### personal_confirmRequest
Confirm specific request

#### personal_rejectRequest
Reject a confirmation request