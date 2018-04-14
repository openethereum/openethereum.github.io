---
title: Permissioning
---

_The development of this feature was supported by the [Energy Web Foundation](http://energyweb.org/) in order to expedite the energy sector revolution towards decentralisation._

## Overview
In a basic [Proof-of-Work chain](Proof-of-Work-Chains.md) all participants are able to perform all roles within the network: connect, mine (validate), send transactions, inspect the state and see all transactions.

At Parity Technologies we are introducing a number of features which enable the network participants to permission different aspects of a blockchain. Often conflated as simply “permissioned blockchains” we introduce permissions on a number of different layers:

* [Network](Permissioning#network.md)
* [Transaction type](Permissioning.md#transaction-type)
* [Validator set](Permissioning.md#validator-set)
* [Gas price](Permissioning.md#gas-price)
* [Private transactions](Private-Transactions.md)

Each user can have different permissions on each layer. All permissioning is based on blockchain accounts, which means that permissions always correspond to an address.

## Network
Permissions on this layer determine which nodes can connect to the network and interact with others. In Parity, individual network members can control their network peers. A Network smart contract enables the governing body to impose any network topology and disallow connections from any external parties.

### How it works
A smart contract has to be deployed that regulates if two nodes can connect to each other given their enode IDs.
The contract must be deployed on the corresponding chain and its address added to the chain spec file under `"params"/"nodePermissionContract"`.

The contract must support the following ABI:
```json
[
  {
    "constant": true,
    "inputs": [
      {
        "name": "sl",
        "type": "bytes32"
      },
      {
        "name": "sh",
        "type": "bytes32"
      },
      {
        "name": "pl",
        "type": "bytes32"
      },
      {
        "name": "ph",
        "type": "bytes32"
      }
    ],
    "name": "connectionAllowed",
    "outputs": [
      {
        "name": "res",
        "type": "bool"
      }
    ],
    "payable": false,
    "type": "function"
  },
  {
    "inputs": [],
    "payable": false,
    "type": "constructor"
  }
]
```
* `sl` is low 32 bytes of peer 1 enode Id
* `sh` is hi 32 bytes of peer 1 enode Id
* `pl` is low 32 bytes of peer 2 enode Id
* `ph` is hi 32 bytes of peer 2 enode Id

Here's a sample contract that implements that ABI:

```
pragma solidity ^0.4.11;

contract PeerManager {
    mapping(bytes32 => bytes32) peers;

    function PeerManager() {
        peers[0x11] = 0x12;
        peers[0x21] = 0x22;
        peers[0x31] = 0x32;
        peers[0x41] = 0x42;
    }

    function connectionAllowed(bytes32 sl, bytes32 sh, bytes32 pl, bytes32 ph) constant returns (bool res) {
        if (sl == 0x01 && sh == 0x02) {
	    return true;
	}
	return pl != 0 && ph != 0 && peers[pl] == ph;
    }
}
```

## Transaction type
Even though all network participants can submit transactions it is still up to the validators to include them. Besides that, it is possible to restrict certain addresses or transaction types on the state transition level.

By transaction types we mean:

* Contract deployment
* Contract interaction
* Simple transfer

The ability of each address to execute any combination of these transaction types can be determined by a contract implementing a special interface.

### How it works
A smart contract has to be deployed that regulates which participant (address) can perform certain types of transactions. The contract must be deployed on the corresponding chain and its address added to the chain spec file under `"params"/"transactionPermissionContract"`.

The contract must support the following ABI:

```json
[
  {
    "constant": false,
    "inputs": [
      {
        "name": "sender",
        "type": "address"
      }
    ],
    "name": "allowedTxTypes",
    "outputs": [
      {
        "name": "",
        "type": "uint32"
      }
    ],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```

* `sender` is the transaction sender address.
* `type` is the transaction type bit mask:

| Type | Bit |
|:---:|:---:|
| Basic transaction | 0x01 |
| Contract call | 0x02 |
| Contract creation | 0x04 |

Here's a sample contract that implements that ABI:
```solidity
pragma solidity ^0.4.11;

contract TestOOG {
    /// Allowed transaction types mask
    uint32 constant None = 0;
    uint32 constant All = 0xffffffff;
    uint32 constant Basic = 0x01;
    uint32 constant Call = 0x02;
    uint32 constant Create = 0x04;
    uint32 constant Private = 0x08;

    function allowedTxTypes(address sender) public returns (uint32)
    {
        if (sender == 0x7e5f4552091a69125d5dfcb7b8c2659029395bdf) return All; // Secret: 0x00..01
        if (sender == 0x2b5ad5c4795c026514f8317c7a215e218dccd6cf) return Basic | Call; // Secret: 0x00..02
        if (sender == 0x6813eb9362372eef6200f3b1dbc3f819671cba69) return Basic; // Secret: 0x00..03
        return None;
    }
}
```


## Validator set
This level of permissions is a rather important one. It determines which parties (Validators) are entitled to create new blocks and thereby build the blockchain. Validators need to collect and validate transactions before sealing them into blocks. Rules according to which they interact can be referred to as a consensus engine. Parity currently supports three different consensus engines:

* Ethash (PoW)
* Aura
* Tendermint (PBFT)

More are being implemented.

For each consensus engine, there are two main varieties of permissioned validation: Proof-of-Authority and Proof-of-Stake. In Proof-of-Authority, validators typically represent some real world entities, which prevents Sybil attacks. These authorities can be added and removed according to a set of rules, such as via a voting process. The rules are specified in a smart contract on the blockchain. Proof-of-Stake on the other hand, relies on security deposits. This means that validators are added after submitting a sufficient amount of valuable tokens, which can be taken away in the case of misbehaviour.

In both cases, Parity Ethereum is able to automatically detect faults in the consensus process and respond immediately. Two types of misbehaviour are possible: malicious and benign. When a malicious misbehaviour is detected by a node, a proof of misbehaviour can be provided to the contract. Benign misbehaviour is more speculative: a node can be never sure if it actually occurred (e.g. differentiating between downtime and a network partition).

In Parity Ethereum a Validator Set can be specified using a contract implementing a special interface. Thanks to the smart contract definition authorities can be managed according to any rules suitable to the particular application.

### How it works
Please see the [Validator Set wiki page](Validator-Set.md).

## Gas price
Currently, Parity already allows whitelisting of accounts for zero gas price transactions. In addition to that, a way to specify gas prices per account will be possible soon. This is achieved by managing gas prices in a smart contract and enables one to regulate how much each account has to spend on interactions with the blockchain. Of course, gas prices can also be set to zero.

### How it works
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
