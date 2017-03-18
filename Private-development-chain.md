**Available only in 1.5 and above.**

Parity provides an `dev` engine, which can be used for dapp development and demos. When used the transactions will be instantly "mined" (after being confirmed by [Secure Transaction Signer](Secure Transaction Signer)) and all blocks will be accepted. It can be simply activated by running:
```
parity --chain dev
```
Or by using a custom [config file](Configuring-Parity#config-file):
```toml
[parity]
chain = "dev"
```

The address created with an empty phrase contains a lot of tokens that can be used to send transactions, it can be created with UI or a RPC call (make sure to enable the `parity_accounts` API):
```
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"parity_newAccountFromPhrase","params":["","password"],"id": 1}' localhost:8545
```
or using the UI: 
 
1. Go to `localhost:8080` in your browser and go through the initial setup.
2. Click on "NEW ACCOUNT".
3. Pick "Recover account from recovery phrase".
4. Leave the phrase empty and fill the other fields.
5. Go to the newly created account and click "TRANSFER" to conduct your first transaction.
6. Confirm with a password picked earlier.
7. A block should get sealed (see in the log) straight away.

When using with [browser-solidity](https://ethereum.github.io/browser-solidity) Chrome extension needs to be installed.

## Customizing the development chain

The default configuration should work fine in most cases, however it can be customized. The following example spec can be passed to `--chain` option where `accounts` contains a custom account with lots of Ether.

In Parity 1.5:
```
{
	"name": "DevelopmentChain",
	"engine": {
		"instantSeal": null
	},
	"params": {
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

In Parity 1.6 and above:
```
{
	"name": "DevelopmentChain",
	"engine": {
		"instantSeal": { "params": {} }
	},
	"params": {
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
