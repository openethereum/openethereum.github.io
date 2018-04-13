---
title: Private Development Chain
---

Parity provides an `dev` engine, which can be used for dapp development and demos. When used the transactions will be instantly "mined" (after being confirmed by [Parity Wallet](Parity-Wallet) or signed) and all blocks will be accepted. It can be simply activated by running:
```
parity --config dev
```

A address containing a lot of tokens (`0x00a329c0648769a73afac7f9381e08fb43dbea72`) should be automatically added with a password being an empty string.

When using with [browser-solidity](https://ethereum.github.io/browser-solidity) Chrome extension needs to be installed.

## Customizing the development chain

The default configuration should work fine in most cases, however it can be customized. The following example spec can be passed to `--chain` option where `accounts` contains a custom account with lots of Ether.

To make multiple transactions confirm at the same time use `--reseal-min-period 0` and to make transactions free use `--gasprice 0`.

```
{
	"name": "DevelopmentChain",
	"engine": {
		"instantSeal": null
	},
	"params": {
		"gasLimitBoundDivisor": "0x0400",
		"accountStartNonce": "0x0",
		"maximumExtraDataSize": "0x20",
		"minGasLimit": "0x1388",
		"networkID" : "0x11"
	},
	"genesis": {
		"seal": {
			"generic": "0x0"
		},
		"difficulty": "0x20000",
		"author": "0x0000000000000000000000000000000000000000",
		"timestamp": "0x00",
		"parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"extraData": "0x",
		"gasLimit": "0x5B8D80"
	},
	"accounts": {
		"0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
		"0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
		"0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
		"0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
		"0x00a329c0648769a73afac7f9381e08fb43dbea72": { "balance": "1606938044258990275541962092341162602522202993782792835301376" }
	}
}
```

## Vagrant
There is a [Vagrant Box](https://github.com/jesuscript/vagrant-eth-env) that creates and starts a preconfigured Parity node running this chain, in case you are looking for a minimal configuration setup.
