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

**Available since 1.6.**
## Non-reporting contract
The list can be also a part of the blockchain state by being stored in an Ethereum contract. The contract has to have the following interface:
```json
{"constant":true,"inputs":[],"name":"getValidators","outputs":[{"name":"","type":"address[]"}],"payable":false,"type":"function"}
```
The function `getValidators` will be called on every block to determine the current list. The switching rules are then determined by the contract implementing that method. The spec should contain the contract address:

```json
"validators": {
    "safeContract": "0x0000000000000000000000000000000000000005"
}
```

Example contracts can be found [here](https://github.com/ethcore/contracts/tree/master/validator_contracts). It is best to include the contract in the genesis placing the bytecode as a "constructor" in the "accounts" field like so:

```json
"0x0000000000000000000000000000000000000005": { "balance": "1", "constructor" : "0x..." }
```

## Reporting contract

Sometimes one might want to automatically take action when one of the validators behaves badly. The definition of bad behaviour depends on a consensus engine and there are two types of bad behaviour:
- benign misbehaviour
- malicious misbehaviour

Benign misbehaviour in Aura may be simply not receiving a block from a designated primary, while malicious misbehaviour would be releasing two different blocks for the same step.

This type of contract can listen to misbehaviour reports from the consensus engine and decide what are the consequences for the validators.

The correct interface is:
```json
[
    {"constant":true,"inputs":[],"name":"getValidators","outputs":[{"name":"","type":"address[]"}],"payable":false,"type":"function"},
    {"constant":false,"inputs":[{"name":"validator","type":"address"}],"name":"reportMalicious","outputs":[],"payable":false,"type":"function"},
    {"constant":false,"inputs":[{"name":"validator","type":"address"}],"name":"reportBenign","outputs":[],"payable":false,"type":"function"}
]
```

It is specified as:

```json
"validators": {
    "contract": "0x0000000000000000000000000000000000000005"
}
```