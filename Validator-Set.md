---
title: Validator Sets
---

A number of Engines available in Parity achieve consensus by referring to a list of "validators" (referred to as authorities if they are linked to physical entities). Validators are a group of accounts which are allowed to participate in the consensus, they validate the transactions and blocks to later sign messages about them. The validator set can be specified in a number of different ways.

## Immutable list

A simple list of addresses specified at genesis.

```json
"validators": {
    "list": [
        "0x7d577a597b2742b498cb5cf0c26cdcd726d39e6e",
        "0x82a978b3f5962a5b0957d9ee9eef472ee55b42f1"
    ]
}
```

## Contracts
**Available since 1.7.**

The list can be also a part of the blockchain state by being stored in an Ethereum contract.

Example contracts can be found [here](https://github.com/paritytech/contracts/tree/master/validator_contracts).

It is best to include the contract in the genesis placing the bytecode as a "constructor" in the "accounts" field like so:

```json
"0x0000000000000000000000000000000000000005": { "balance": "1", "constructor" : "0x..." }
```

### Non-reporting contract
A simple validator contract has to have the following interface:
```json
[{"constant":false,"inputs":[],"name":"finalizeChange","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValidators","outputs":[{"name":"_validators","type":"address[]"}],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_parent_hash","type":"bytes32"},{"indexed":false,"name":"_new_set","type":"address[]"}],"name":"InitiateChange","type":"event"}]
```

which corresponds to this Solidity contract definition:
```solidity
contract ValidatorSet {
    /// Issue this log event to signal a desired change in validator set.
    /// This will not lead to a change in active validator set until
    /// finalizeChange is called.
    ///
    /// Only the last log event of any block can take effect.
    /// If a signal is issued while another is being finalized it may never
    /// take effect.
    ///
    /// _parent_hash here should be the parent block hash, or the
    /// signal will not be recognized.
    event InitiateChange(bytes32 indexed _parent_hash, address[] _new_set);

    /// Get current validator set (last enacted or initial if no changes ever made)
    function getValidators() constant returns (address[] _validators);

    /// Called when an initiated change reaches finality and is activated.
    /// Only valid when msg.sender == SUPER_USER (EIP96, 2**160 - 2)
    ///
    /// Also called when the contract is first enabled for consensus. In this case,
    /// the "change" finalized is the activation of the initial set.
    function finalizeChange();
}
```

There is a notion of an "active" validator set: this is the set of the most recently finalized signal (InitiateChange event). The initial set is

The function `getValidators` should always return the active set or the initial set if the contract hasn't been activated yet.
Switching the set should be done by issuing a `InitiateChange` event with the parent block hash and new set, storing the pending set, and then waiting for call to `finalizeChange` (by the `SYSTEM_ADDRESS`: `2^160 - 2`) before setting the active set to the pending set. This mechanism is used to ensure that the previous validator set "signs off" on the changes before activation, leading to full security in situations like warp and light sync, where state transitions aren't checked.

Other than these restrictions, the switching rules are fully determined by the contract implementing that method. The spec should contain the contract address:

```json
"validators": {
    "safeContract": "0x0000000000000000000000000000000000000005"
}
```

### Reporting contract

Sometimes one might want to automatically take action when one of the validators behaves badly. The definition of bad behaviour depends on a consensus engine and there are two types of bad behaviour:
- benign misbehaviour
- malicious misbehaviour

Benign misbehaviour in Aura may be simply not receiving a block from a designated primary, while malicious misbehaviour would be releasing two different blocks for the same step.

This type of contract can listen to misbehaviour reports from the consensus engine and decide what are the consequences for the validators.

The correct interface is:
```json
[{"constant":false,"inputs":[],"name":"finalizeChange","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValidators","outputs":[{"name":"_validators","type":"address[]"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"},{"name":"blockNumber","type":"uint256"},{"name":"proof","type":"bytes"}],"name":"reportMalicious","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"},{"name":"blockNumber","type":"uint256"}],"name":"reportBenign","outputs":[],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_parent_hash","type":"bytes32"},{"indexed":false,"name":"_new_set","type":"address[]"}],"name":"InitiateChange","type":"event"}]
```

which corresponds to this Solidity contract definition:
```solidity
contract ReportingValidatorSet {
    // all same as ValidatorSet
    event InitiateChange(bytes32 indexed _parent_hash, address[] _new_set);

    function getValidators() constant returns (address[] _validators);
    function finalizeChange();

    // Reporting functions: operate on current validator set.
    // malicious behavior requires proof, which will vary by engine.

    function reportBenign(address validator, uint256 blockNumber);
    function reportMalicious(address validator, uint256 blockNumber, bytes proof);
}
```

`InitiateChange`, `getValidators` and `finalizeChange` should function exactly as in a non-reporting contract.
There are two new functions, `reportBenign` and `reportMalicious`. Each should come with the address of a validator being reported and the block number at which misbehavior occurred. `reportMalicious` also requires a proof of malice, which is an arbitrary byte-string which different engines will set to different values. Validators will call these when they detect misbehavior.

These should function on only the current active validator set.

It is specified as:

```json
"validators": {
    "contract": "0x0000000000000000000000000000000000000005"
}
```

## Multi set
**Available only in 1.6 and above.**
This validator set can specify any combination of other validator sets. Switching is done based on the number of the current block. It can be useful for conducting chain forks. First set has to start at block 0.

```json
"validators": {
    "multi": {
        "0": {
            "list": [
                "0x82a978b3f5962a5b0957d9ee9eef472ee55b42f1",
                "0x7d577a597b2742b498cb5cf0c26cdcd726d39e6e"
            ]
        },
        "100": {
            "contract": "0x0000000000000000000000000000000000000005"
        }
    }
}
```

Note that transitions to a contract will not take effect immediately. Rather, they will take effect when the transition block is finalized by the previous set of validators. Again, this is to provide full security for warp and light sync.

Transitions to a fixed list will take effect immediately because regardless of whether an attacker gives a light client a transition block with an invalid state, the subsequent validator set will always be the same.
