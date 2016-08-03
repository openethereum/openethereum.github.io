Parity provides an `InstantSeal` engine, which can be used for dapp development and demos. When used the transactions will be instantly "mined" (after being confirmed by [Secure Transaction Signer](https://github.com/ethcore/parity/wiki/Secure-Transaction-Signer)) and all blocks will be accepted.

To use it, the following example spec can be passed to `--chain` option where `accounts` contains a custom account with lots of Ether.
```
{
	"name": "Development",
	"engine": {
		"InstantSeal": null
	},
	"params": {
		"accountStartNonce": "0x0100000",
		"maximumExtraDataSize": "0x20",
		"minGasLimit": "0x1388",
		"networkID" : "0x2"
	},
	"genesis": {
		"seal": {
			"ethereum": {
				"nonce": "0x00006d6f7264656e",
				"mixHash": "0x00000000000000000000000000000000000000647572616c65787365646c6578"
			}
		},
		"difficulty": "0x20000",
		"author": "0x0000000000000000000000000000000000000000",
		"timestamp": "0x00",
		"parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"extraData": "0x",
		"gasLimit": "0x2fefd8"
	},
	"accounts": {
		"0000000000000000000000000000000000000001": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
		"0000000000000000000000000000000000000002": { "balance": "1", "nonce": "1048576", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
		"0000000000000000000000000000000000000003": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
		"0000000000000000000000000000000000000004": { "balance": "1", "nonce": "1048576", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
    "16a9dfd266e3229f05a2704c13bf2e16ea23e7c3": { "balance": "1606938044258990275541962092341162602522202993782792835301376", "nonce": "1048576" }
	}
}
```
When using with [browser-solidity](https://ethereum.github.io/browser-solidity) `--jsonrpc-cors '*'` option has to be added as well.