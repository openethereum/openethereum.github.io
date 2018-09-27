---
title: Pluggable Consensus
---

Parity comes bundled with a number of consensus engines. While the most widely used is the `Ethash` proof of work `Engine`, there are others which can be used for proof of authority or stake chains.
The `Engine` is chosen by placing an appropriate entry in the `"engine"` field of the spec and providing the correct `"seal"` under `"genesis"` field, as described on the [Chain specification](Chain-specification.md) page.

## Ethash

[Original Ethereum PoW](https://github.com/ethereum/wiki/wiki/Ethash) `Engine`.

```json
"engine": {
  "ethash": {
    "params": {
      "minimumDifficulty": "0x020000",
      "difficultyBoundDivisor": "0x0800",
      "durationLimit": "0x0d",
      "blockReward": "0x4563918244F40000",
      "homesteadTransition": 0,
      "eip150Transition": 0,
      "eip160Transition": 10,
      "eip161abcTransition": 10,
      "eip161dTransition": 10,
      "maxCodeSize": 24576
    }
  }
}
```

The `"params"` object for `"Ethash"` may contain the following keys (YP refers to the Yellow Paper equation numbers):
- `"minimumDifficulty"` is self-explanatory, redefines the value of `D_0` in the latter two lines of (YP:39)
- `"difficultyBoundDivisor"` influences how rapidly is the difficulty evolving, redefines the value in (YP:41), originally 2048 = `0x800`
- `"durationLimit"` is the equilibrium block interval for the pre-Homestead era difficulty evolution, irrelevant for block numbers above `homesteadTransition`, redefines the value in (YP:42), originally 13 = `0xd`
- `"difficultyIncrementDivisor"` is related to the equilibrium block intervals for the Homestead era difficulty evolution, redefines the value in (YP:43), originally 10 = `0xa`
- `"homesteadTransition"` is `N_H` of (YP:13, 39) etc., or the block number after which the second line of (YP:39) is superseded by the third line, changing the difficulty evolution function
- `"blockReward"` specifies the reward in wei given for authoring a block
- `"blockRewardContractTransition"` Block at which the block reward contract should start being used.
- `"blockRewardContractAddress"` Block reward contract address, setting the block reward contract. This option overrides the static block reward definition.
- `"blockRewardContractCode"` Bytecode of the block reward contract. This option overrides `"BlockRewardContractAddress"`. In this mode, code are executed in static call, so it must be pure.

```json
"seal": {
  "ethereum": {
    "nonce": "0x0000000000000042",
    "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
  }
}
```

## Instant Seal

`Engine` which can be used for development, as described in [Private development chain](Private-development-chain.md).

```json
"engine": {
  "instantSeal": {
    "params": {}
  }
}
```

```json
"seal": {
  "generic": "0x0"
}
```

The `"params"` object for `"instantSeal"` may contain the following keys:

- `"millisecondTimestamp"` if true, then use millisecond timestamp instead of second timestamp. This sometimes avoids timestamp collusion if sending more than one transaction per second.

## Validator engines

The following `Engine`s achieve consensus by referring to a list of "validators" (referred to as authorities, when they are linked to physical entities). Validator set is a group of accounts which are allowed to participate in the consensus, they validate the transactions and blocks to later sign messages about them.

In the simplest case they can be specified at genesis using a simple `"list"` (as shown in the Authority Round section):
```json
"validators": {
  "list": [
    "0x7d577a597b2742b498cb5cf0c26cdcd726d39e6e",
    "0x82a978b3f5962a5b0957d9ee9eef472ee55b42f1"
  ]
}
```

More information can be found on the [Validator Set](Validator-Set.md) page.


## Aura

Simple and fast consensus algorithm, each validator gets an assigned time slot in which they can release a block. The time slots are determined by the system clock of each validator. [More details](Aura.md).

```json
"engine": {
    "authorityRound": {
        "params": {
            "stepDuration": "5",
            "validators" : {
                "multi": {
                        "0": { "list": ["0xc6d9d2cd449a754c494264e1809c50e34d64562b"] },
                        "10": { "list": ["0xd6d9d2cd449a754c494264e1809c50e34d64562b"] },
                        "20": { "contract": "0xc6d9d2cd449a754c494264e1809c50e34d64562b" }
                }
            }
        }
    }
}
```

`"stepDuration"` determines the lowest interval between blocks in seconds, too low might cause reorgs if the system clocks are not synchronized, too high leads to slow block issuance

- `"validators"` 
  - `"list"` is the list of addresses of the entities which will be allowed to issue blocks
  - `"safeContract"` Address of a contract that indicates the list of authorities.
  - `"contract`" Address of a contract that indicates the list of authorities and enables [reporting their misbehaviors](Validator-Set.md#reporting-contract) using transactions.
  - `"multi`" A map of starting blocks for each validator set (see example above).
 
Optional:

- `"blockReward"` Determines the reward given to issuing authority (in Wei).
- `"validateScoreTransition"` Optional, will be included for block 0 by default - Block after which a block's difficulty is verified.
- `"validateStepTransition"` Block after which a double block proposing - e.g when parent and current block are equal - is invalid and considered as a malicious behavior.
- `"immediateTransitions"` - bool - Determines whether the validator set transition is applied immediately without waiting for finality (`true`) or not (`false`).
- `"blockRewardContractTransition"` Block at which the block reward contract should start being used.
- `"blockRewardContractAddress"` Block reward contract address, setting the block reward contract. This option overrides the static block reward definition.
- `"blockRewardContractCode"` Bytecode of the block reward contract. This option overrides `"BlockRewardContractAddress"`. In this mode, code are executed in static call, so it must be pure.
- `"maximumUncleCountTransition"` Block at which maximum uncle count should be considered.
- `"maximumUncleCount"` Maximum number of accepted uncles.
- `"emptyStepsTransition"` Block at which empty step messages should start.
- `"maximumEmptySteps"` Maximum number of accepted empty steps.


```json
"seal": {
    "authorityRound": {
        "step": "0x0",
        "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
    }
}
```

The genesis seal should not be changed unless a hard fork is conducted.

If malicious authorities are possible then `--force-sealing` is advised, this will ensure that the correct chain is the longest (making it BFT with finality of authorities_count * step_duration given no network partitions).
