# The `signer` Module

## JSON-RPC methods

* [signer_generateAuthorizationToken](#signer_generateauthorizationtoken)
* [signer_requestsToConfirm](#signer_requeststoconfirm)
* [signer_confirmRequest](#signer_confirmrequest)
* [signer_confirmRequestRaw](#signer_confirmrequestraw)
* [signer_rejectRequest](#signer_rejectrequest)

## JSON RPC API Reference


#### signer_generateAuthorizationToken
Generates a new authorization token

##### Returns
TODO


#### signer_requestsToConfirm
Returns a list of items to confirm.

##### Returns
TODO


#### signer_confirmRequest
Confirm specific request

##### Parameters
1. `DATA`, 32 Bytes - A request id (TODO)
2. `TransactionModification` - TODO
3. `STR` - Password to use to unlock the account

##### Returns
TODO


#### signer_confirmRequestRaw
Confirm specific request with signed result

##### Parameters
1. `DATA`, 32 Bytes - A request id (TODO)
2. `DATA` - TODO

##### Returns
TODO

#### signer_rejectRequest
Reject a confirmation request

##### Parameters
1. `DATA`, 32 Bytes - A request id (TODO)

##### Returns
TODO