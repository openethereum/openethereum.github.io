---
title: Service Transaction Checker Contract
---

Service transaction checker contract is used by Parity to filter out transactions with zero gas price (aka service transactions).

Default behaviour (to which you can always revert by using `--refuse-service-transactions` command line option) is to discard all service transactions, coming from network. If ['registrar'](https://github.com/paritytech/contracts/blob/master/Registry.sol) contract is deployed and registered for your chain, you can alter default behaviour by:
1) deploying ['certifier'](https://github.com/paritytech/contracts/blob/master/SimpleCertifier.sol) contract
2) registering address of this contract in registry with 'service_transaction_checker' name
On startup, Parity will check if this contract is registered and will start checking author of each service transaction, coming from network. If author is **not** certified to create service transactions, transaction will be discarded. Otherwise, it will be accepted.

To register address, which is able to create service transactions, you should use `certify` method. To reverse this action, use `revoke` method. You can use `certified` method to check if address is certified for making service transactions.

Below is ABI of service transaction checker contract:
```
[
	{"constant":false,"inputs":[{"name":"_new","type":"address"}],"name":"setOwner","outputs":[],"payable":false,"type":"function"},
	{"constant":false,"inputs":[{"name":"_who","type":"address"}],"name":"certify","outputs":[],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"_who","type":"address"},{"name":"_field","type":"string"}],"name":"getAddress","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},
	{"constant":false,"inputs":[{"name":"_who","type":"address"}],"name":"revoke","outputs":[],"payable":false,"type":"function"},
	{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},
	{"constant":true,"inputs":[],"name":"delegate","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"_who","type":"address"},{"name":"_field","type":"string"}],"name":"getUint","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},
	{"constant":false,"inputs":[{"name":"_new","type":"address"}],"name":"setDelegate","outputs":[],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"_who","type":"address"}],"name":"certified","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"_who","type":"address"},{"name":"_field","type":"string"}],"name":"get","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"}
]
```
