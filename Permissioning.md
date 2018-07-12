---
title: Permissioning
---

_The development of this feature was supported by the [Energy Web Foundation](http://energyweb.org/) in order to expedite the energy sector revolution towards decentralisation._

## Overview
In a basic [Proof-of-Work chain](Proof-of-Work-Chains.md) all participants are able to perform all roles within the network: connect, mine (validate), send transactions, inspect the state and see all transactions.

At Parity Technologies, we are introducing a number of features which enable the network participants to permission different aspects of a blockchain. Often conflated as simply “permissioned blockchains” we introduce permissions on a number of different layers:

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
The contract must be deployed on the corresponding chain and its address added to the [chain spec file](Chain-specification) under `"params"/"nodePermissionContract"`.

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

The EVM architecture allows 32 bytes words. As enodes addresses are 64 bytes long, they need to be cut in two parts for a smart contract to handle them. For example the enode `enode://841015562d43c8037b127ee2a89f861d39beb468fecab72ad4bf369d3db8a01a5adeee0e0422cb021acea7ffeb0516db9e1211510ad353dc353b8c52165003c8` would be represented using :
- `sl: 0x841015562d43c8037b127ee2a89f861d39beb468fecab72ad4bf369d3db8a01a`
- `sh: 0x5adeee0e0422cb021acea7ffeb0516db9e1211510ad353dc353b8c52165003c8`


Here's a sample contract that implements this ABI. It contains 4 nodes and a definition of which nodes can connect to each other. In this example :
- node 1 can connect to any other node. 
- node 2 can connect to any other node except node 3.
- node 3 can connect to any other node except node 2 and 4.
- node 4 can connect to any other node except node 3.


```
pragma solidity ^0.4.11;

contract PeerManager {
    struct PeerInfo {
        bytes32 public_low;
        bytes32 public_high;
    }

    mapping(uint => PeerInfo) peers;

    bool[5][5] allowedConnections;

    function PeerManager() {
        peers[0] = PeerInfo(0x0a6e6d6729e9d185a575e867cd1f2b5557032fe9018e50fff328d0cbafd407c3, 0x28c7585ce5a69a136d1fe8427671b8a07633edb2f8371904d5123bc70d899983);
        peers[1] = PeerInfo(0x85d0762d12a46b9ba6405eb36d440d4e04b8f99fd44f11e83eaacba5f690ff29, 0xe4af4ac11963719811e7bbf07f876e2e5ff211475bfaa862425ddb2261f2e861);
        peers[2] = PeerInfo(0xa526541d1ae9460b4f01142d07d7fca57fef99cd14b477fe1c4facf29bd13375, 0x4c6fab9f6d8926f249cf79239309fb7923cc8ed31661fe01d40aa76689738e84);
        peers[3] = PeerInfo(0x66279502cbf87e25bb915a6dfd59c79d4cbb5f848ee5327c43d55ba63682c809, 0x2b88f55414bcb53e691c8044cd72c7bffe534eb8a8f17bebee34ef63b526e487);
        peers[4] = PeerInfo(0x96503b42181b03c632152e53d7a2a10851ec06e81dfa2dca9fb736c0f0a62f32, 0x8918d93907d19b1813ae8cc9e36e41f77b936040ab4228aeadf9108cb1ac587f);

        allowedConnections[0][0] = true;
        allowedConnections[0][1] = true;
        allowedConnections[0][2] = true;
        allowedConnections[0][3] = true;
        allowedConnections[0][4] = true;
        allowedConnections[1][0] = true;
        allowedConnections[1][1] = true;
        allowedConnections[1][2] = true;
        allowedConnections[1][3] = true;
        allowedConnections[1][4] = true;
        allowedConnections[2][0] = true;
        allowedConnections[2][1] = true;
        allowedConnections[2][2] = true;
        allowedConnections[2][3] = false;
        allowedConnections[2][4] = true;
        allowedConnections[3][0] = true;
        allowedConnections[3][1] = true;
        allowedConnections[3][2] = false;
        allowedConnections[3][3] = true;
        allowedConnections[3][4] = false;
        allowedConnections[4][0] = true;
        allowedConnections[4][1] = true;
        allowedConnections[4][2] = true;
        allowedConnections[4][3] = false;
        allowedConnections[4][4] = true;
    }

    function connectionAllowed(bytes32 sl, bytes32 sh, bytes32 pl, bytes32 ph) constant returns (bool res) {
        uint index1 = 0;
        bool index1_found = false;
        uint index2 = 0;
        bool index2_found = false;
        for (uint i = 0; i < 5; i++) {
            PeerInfo storage peer = peers[i];

            if (sh == peer.public_high && sl == peer.public_low) {
                index1 = i;
                index1_found = true;
            }

            if (ph == peer.public_high && pl == peer.public_low) {
                index2 = i;
                index2_found = true;
            }
        }

        if (!index1_found || !index2_found) {
            return false;
        }

        return allowedConnections[index1][index2];
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
A smart contract has to be deployed to regulate which participant (address) can perform certain types of transactions. The contract must be deployed on the corresponding chain and its address added to the chain spec file under `"params"/"transactionPermissionContract"`. Parity client will use this smart contract locally and call the `allowedTxTypes` method for every transaction to decide whether the sender is allowed to perform it. Since the `allowedTxTypes` method is called by Parity client during the local permission check (no transaction is broadcasted to the network), any state modification made during this call will not be visible on-chain.

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

For each consensus engine, there are two main varieties of permissioned validation: Proof-of-Authority and Proof-of-Stake. In Proof-of-Authority, validators typically represent some real-world entities, which prevents Sybil attacks. These authorities can be added and removed according to a set of rules, such as via a voting process. The rules are specified in a smart contract on the blockchain. Proof-of-Stake, on the other hand, relies on security deposits. This means that validators are added after submitting a sufficient amount of valuable tokens, which can be taken away in the case of misbehaviour.

In both cases, Parity Ethereum is able to automatically detect faults in the consensus process and respond immediately. Two types of misbehaviour are possible: malicious and benign. When a malicious misbehaviour is detected by a node, a proof of misbehaviour can be provided to the contract. Benign misbehaviour is more speculative: a node can be never sure if it actually occurred (e.g. differentiating between downtime and a network partition).

In Parity Ethereum a Validator Set can be specified using a contract implementing a special interface. Thanks to the smart contract definition authorities can be managed according to any rules suitable to the particular application.

### How it works
Please see the [Validator Set wiki page](Validator-Set.md).

## Gas price
Currently, Parity already allows whitelisting of accounts for zero gas price transactions. In addition to that, a way to specify gas prices per account will be possible soon. This is achieved by managing gas prices in a smart contract and enables one to regulate how much each account has to spend on interactions with the blockchain. Of course, gas prices can also be set to zero.

### How it works
Service transaction checker contract is used by Parity to filter out transactions with zero gas price (aka service transactions).

The default behaviour (to which you can always revert by using `--refuse-service-transactions` command line option) is to discard all service transactions, coming from the network. If the ['registry'](https://github.com/parity-contracts/name-registry/blob/master/contracts/Registry.sol) contract is deployed and registered for your chain, you can alter the default behaviour by:
1) deploying ['certifier'](https://github.com/parity-contracts/name-registry/blob/master/contracts/SimpleCertifier.sol) contract
2) registering the address of this contract under the name 'service_transaction_checker'
On startup, Parity will check if this contract is registered and will start checking the author of each service transaction, coming from the network. If the author is **not** certified to create service transactions, the transaction will be discarded. Otherwise, it will be accepted.

To register an address and allow it to create service transactions, you should use the `certify` method. To revert this action, use the `revoke` method. Use the `certified` method to check if an address is certified.

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
