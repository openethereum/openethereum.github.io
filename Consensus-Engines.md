Parity comes bundled with a number of consensus engines. While the most widely used is the `Ethash` proof of work `Engine`, there are others which can be used for proof of authority or stake chains.
The `Engine` is chosen by placing an appropriate entry in the `"engine"` field of the spec and providing the correct `"seal"` under `"genesis"` field, as described on the [[Chain specification]] page.

## Ethash

[Original Ethereum PoW](https://github.com/ethereum/wiki/wiki/Ethash) `Engine`.

```
"engine": {
	"Ethash": {
		"params": {
			"gasLimitBoundDivisor": "0x0400",
			"minimumDifficulty": "0x020000",
			"difficultyBoundDivisor": "0x0800",
			"durationLimit": "0x0d",
			"blockReward": "0x4563918244F40000",
			"registrar": "0x81a4b044831c4f12ba601adb9274516939e9b8a2",
			"homesteadTransition": 0,
			"eip150Transition": 0,
			"eip155Transition": 10,
			"eip160Transition": 10,
			"eip161abcTransition": 10,
			"eip161dTransition": 10,
			"maxCodeSize": 24576
		}
	}
}
```

```
"seal": {
	"ethereum": {
		"nonce": "0x0000000000000042",
		"mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
	}
}
```

## Instant Seal

`Engine` which can be used for development, as described in [[Private development chain]].

```
"engine": {
	"instantSeal": null
}
```

```
"seal": {
	"generic": "0x0"
}
```

This `Engine` does not have any parameters to be adjusted.

## Authority Round

A PoW inspired consensus algorithm, each authority gets an assigned time slot in which they can release a block. The time slots are determined by the system clock of each authority.

```
"engine": {
    "authorityRound": {
        "params": {
            "gasLimitBoundDivisor": "0x400",
            "stepDuration": "5",
            "authorities" : [
                "0x37f93cfe411fa244b87ff257085ee360fca245e8",
                "0x610a3a37b98bf0c91c35442e489c246096739324"
            ]
        }
    }
}
```

`"gasLimitBoundDivisor"` determines how fast the gas limit should adjust, most of the time `0x400` is fine  
`"stepDuration"` determines the lowest interval between blocks in seconds, too low might cause reorgs if the system clocks are not synchronized, too high leads to slow block issuance  
`"authorities"` is the list of addresses of the entities which will be allowed to issue blocks  
Optional:  
`"blockReward"` determines the reward given to issuing authority  

```
"seal": {
    "authorityRound": {
        "step": "0x0",
        "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
    }
}
```

The genesis seal should not be changed unless a hard fork is conducted.

If malicious authorities are possible then --force-sealing is advised, this will ensure that the correct chain is the longest (making it BFT with finality of authorities_count * step_duration given no network partitions).

## Tendermint

This consensus is still experimental, please use Authority Round for more stable solution.

```
"engine": {
    "tendermint": {
        "params": {
            "gasLimitBoundDivisor": "0x400",
            "authorities" : [
                "0x37f93cfe411fa244b87ff257085ee360fca245e8",
                "0x610a3a37b98bf0c91c35442e489c246096739324"
            ]
        }
    }
}
```

`"gasLimitBoundDivisor"` determines how fast the gas limit should adjust, most of the time `0x400` is fine  
`"authorities"` is the list of addresses of the entities which will be allowed to issue blocks  
Optional:  
`"blockReward"` determines the reward given to issuing authority  

```
"seal": {
    "tendermint": {
        "round": "0x0",
        "proposal": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "precommits": [
            "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
        ]
    }
}
```

The genesis seal should not be changed unless a hard fork is conducted.