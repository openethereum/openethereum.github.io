A number of Engines available in Parity achieve consensus by referring to a list of "validators" (referred to as authorities if they are linked to physical entities). Validators are a group of accounts which are allowed to participate in the consensus, they validate the transactions and blocks to later sign messages about them. The validator set can be specified in a number of different ways.

# Immutable list

A simple list of addresses specified at genesis.

```json
"validators": {
    "list": [
        "0x7d577a597b2742b498cb5cf0c26cdcd726d39e6e",
        "0x82a978b3f5962a5b0957d9ee9eef472ee55b42f1"
    ]
}
```

# Contracts
**Available since 1.6.**

The list can be also a part of the blockchain state by being stored in an Ethereum contract.

Example contracts can be found [here](https://github.com/paritytech/contracts/tree/master/validator_contracts).

It is best to include the contract in the genesis placing the bytecode as a "constructor" in the "accounts" field like so:

```json
"0x0000000000000000000000000000000000000005": { "balance": "1", "constructor" : "0x..." }
```

## Non-reporting contract
A simple validator contract has to have the following interface:
```json
[{"constant":true,"inputs":[],"name":"transitionNonce","outputs":[{"name":"nonce","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValidators","outputs":[{"name":"validators","type":"address[]"}],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_parent_hash","type":"bytes32"},{"indexed":true,"name":"_nonce","type":"uint256"},{"indexed":false,"name":"_new_set","type":"address[]"}],"name":"ValidatorsChanged","type":"event"}]
```

which corresponds to this Solidity contract definition:
```solidity
contract ValidatorSet {
    event ValidatorsChanged(bytes32 _parent_hash, uint256 _nonce, address[] _new_set);
    
    function getValidators() constant returns (address[] validators);
    function transitionNonce() constant returns (uint256);
}
```

The function `getValidators` will be called on every block to determine the current list. The switching rules are then determined by the contract implementing that method. The spec should contain the contract address:

```json
"validators": {
    "safeContract": "0x0000000000000000000000000000000000000005"
}
```

### Warp and Light Sync

The function `transitionNonce` and the `ValidatorsChanged` event are for the purposes of supporting warp sync and light clients and can be ignored if these cases are not important to you: just return 0 from `transitionNonce` and never issue a `ValidatorsChanged` event.

To support warp and light sync, the contract should maintain a "transition nonce", which is defined to be the number of blocks in which a change of the validators set has occurred. This should start at 0. Every time the validator set changes, the contract should bump the transition nonce (unless another transition occurred already in the same block) and issue a `ValidatorsChanged(parent_hash, nonce, new_set)` event where:
  - `parent_hash` is the hash of the parent block (`block.blockhash(block.number - 1)` in Solidity)
  - `nonce` is the new transition nonce
  - and `new_set` is the new validator set.

For correct behavior, silent changes should never occur. More strictly, `getValidators()` and `transitionNonce()` calls should always return the `new_set` and `nonce` values of the most recently `ValidatorsChanged` event or their initial values if no change has ever been issued.

`parent_hash` is included to limit the time window an attacker has to make a log which sets the same bits in the bloom filter, forcing the light client to download receipts for a false positive.
The transition nonce may only be incremented once per block because only the result of `getValidators` at the final state of the block matters for consensus, and therefore any transitions don't persist to the end of the block may as well have not happened.

## Reporting contract

Sometimes one might want to automatically take action when one of the validators behaves badly. The definition of bad behaviour depends on a consensus engine and there are two types of bad behaviour:
- benign misbehaviour
- malicious misbehaviour

Benign misbehaviour in Aura may be simply not receiving a block from a designated primary, while malicious misbehaviour would be releasing two different blocks for the same step.

This type of contract can listen to misbehaviour reports from the consensus engine and decide what are the consequences for the validators.

The correct interface is:
```json
[{"constant":true,"inputs":[],"name":"transitionNonce","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValidators","outputs":[{"name":"validators","type":"address[]"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"},{"name":"blockNumber","type":"uint256"},{"name":"proof","type":"bytes"}],"name":"reportMalicious","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"},{"name":"blockNumber","type":"uint256"}],"name":"reportBenign","outputs":[],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":false,"name":"_parent_hash","type":"bytes32"},{"indexed":false,"name":"_nonce","type":"uint256"},{"indexed":false,"name":"_new_set","type":"address[]"}],"name":"ValidatorsChanged","type":"event"}]
```

which corresponds to this Solidity contract definition:
```solidity
contract ReportingValidatorSet {
    event ValidatorsChanged(bytes32 _parent_hash, uint256 _nonce, address[] _new_set);
    
    function getValidators() constant returns (address[] validators);
    function transitionNonce() constant returns (uint256);
    
    function reportBenign(address validator, uint256 blockNumber);
    function reportMalicious(address validator, uint256 blockNumber, bytes proof);
}
```

`ValidatorsChanged`, `getValidators` and `transitionNonce` should function exactly as in a non-reporting contract.
There are two new functions, `reportBenign` and `reportMalicious`. Each should come with the address of a validator being reported and the block number at which misbehavior occurred. `reportMalicious` also requires a proof of malice, which is an arbitrary byte-string which different engines will set to different values.

It is specified as:

```json
"validators": {
    "contract": "0x0000000000000000000000000000000000000005"
}
```

# Multi set
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