By default, when simply running `parity`, Parity Ethereum will connect to the official public Ethereum network. Ethereum uses EVM to perform its state transitions and proof of work to achieve consensus, however Parity makes it possible to run alternative EVM-based chains which can be specified by a JSON chain spec.
In order to run a chain different to the official public Ethereum one, Parity has to be ran with the `--chain` option. There are a few named presets that can be selected from or a custom JSON spec file can be supplied.

## JSON chain spec format
A JSON file which specifies rules of a blockchain, some fields are optional which are described following the minimal example, these default to 0.
```
{
	"name": "CHAIN_NAME",
	"engine": {
		"ENGINE_NAME": {
			"params": {
				ENGINE_PARAMETERS
			}
		}
	},
	"genesis": {
		"seal": {
			ENGINE_SPECIFIC_GENESIS_SEAL
		},
		"difficulty": "0x20000",
		"gasLimit": "0x2fefd8"
	},
	"params": {
			"networkID" : "0x2"
			"maximumExtraDataSize": "0x20",
			"minGasLimit": "0x1388",
	},
	"accounts": {
		GENESIS_ACCOUNTS
	}
}
```

`"name"` field contains any name used to identify the chain.

`"engine"` field describes the consensus engine used for a particular chain, details are explained in the [Consensus Engines] section.

`"genesis"` contains the genesis block (first block in the chain) header information. `"seal"` is consensus engine specific and is further described in [Consensus Engines] section.

`"params"` contains general chain parameters.

`"networkID"` DevP2P supports multiple networks, and ID is used to uniquely identify each one which is used to connect to correct peers and to prevent transaction replay across chains.

`"maximumExtraDataSize"` determines how much extra data the block issuer can place in the block header.

`"minGasLimit"` gas limit can adjust across blocks, this parameter determines the absolute minimum it can reach.

Optional:
`"accountStartNonce"` 

`"accounts"` contains optional contents of the genesis block, such as simple accounts with balances and usually the standard Ethereum builtin contracts will be present which enable usage of Solidity programming language.
