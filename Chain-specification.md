---
title: Chain Specification
---

By default, when simply running `parity`, Parity Ethereum will connect to the official public Ethereum network.

In order to run a chain different to the official public Ethereum one, Parity has to run with the `--chain` option or with a [config file](Configuring-Parity-Ethereum.md#config-file) specifying `chain = "path"` under `[parity]`. There are a few named presets that can be selected from or a custom JSON spec file can be supplied.

## Chain presets available
- [`mainnet`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/foundation.json) (default) main Ethereum network
- [`kovan` or `testnet`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/kovan.json) the [fast Ethereum test network](https://github.com/kovan-testnet/config)
- [`ropsten`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/ropsten.json) the old Ethereum test network
- [`classic`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/classic.json) Ethereum Classic network
- [`classic-testnet`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/morden.json) original Morden testnet and current Ethereum Classic testnet
- [`expanse`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/expanse.json) Expanse network
- [`dev`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/instant_seal.json) a [Private development chain](Private-development-chain) to be used locally, submitted transactions are inserted into blocks instantly without the need to mine
- [`musicoin`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/musicoin.json) Musicoin network
- [`ellaism`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/ellaism.json) Ellaism network
- [`tobalaba`](https://github.com/paritytech/parity-ethereum/blob/master/ethcore/res/ethereum/tobalaba.json) EWF Tobalaba network

## Private chains

Parity supports private chain and private network configuration via [Chain specification](Chain-specification.md) file provided with `--chain`. In addition to the usual [Proof of Work Chains](Proof-of-Work-Chains.md), Parity also includes [Proof of Authority Chains](Proof-of-Authority-Chains.md) which do not require mining.
More details on the available options can be found on the [Pluggable Consensus](Pluggable-Consensus.md) page.

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
			"networkID" : "0x2",
			"maximumExtraDataSize": "0x20",
			"minGasLimit": "0x1388"
	},
	"accounts": {
		GENESIS_ACCOUNTS
	}
}
```

+ **`"name"`** field contains any name used to identify the chain. It is used as a folder name for database files. If it contains spaces, the `"dataDir"` parameter without spaces is required (see "Optional spec fields" below).

+ **`"engine"`** field describes the consensus engine used for a particular chain, details are explained in the [Consensus Engines](Pluggable-Consensus.md) section.

+ **`"genesis"`** contains the genesis block (first block in the chain) header information.
  + `"seal"` is consensus engine specific and is further described in [Consensus Engines](Pluggable-Consensus.md).
  + `"difficulty"` is the difficulty of the genesis block. This parameter is required for any type of chain but can be of arbitrary value if a PoA engine is used.
  + `"gasLimit"` is the gas limit of the genesis block. It affects the initial gas limit adjustment.

  Optional:
  + `"author"` address of the genesis block author.
  + `"timestamp"` UNIX timestamp of the genesis block.
  + `"parentHash"` hash of the genesis "parent" block.
  + `"transactionsRoot"` root of the genesis block's transactions trie
  + `"receiptsRoot"` root of the genesis block's receipts trie.
  + `"stateRoot"` genesis state root, calculated automatically from the `"accounts"` field.
  + `"gasUsed"` gas used in the genesis block.
  + `"extraData"` extra data of the genesis block.

+ **`"params"`** contains general chain parameters:
  + `"networkID"` DevP2P supports multiple networks, and ID is used to uniquely identify each one which is used to connect to correct peers and to prevent transaction replay across chains.
  + `"maximumExtraDataSize"` determines how much extra data the block issuer can place in the block header.
  + `"minGasLimit"` gas limit can adjust across blocks, this parameter determines the absolute minimum it can reach.
    
  Optional:
  + `"accountStartNonce"` in the past this was used for transaction replay protection
  + `"chainID"` chain identifier, if not present then equal to `networkID`
  + `"subprotocolName"` by default its the `eth` subprotocol
  + `"forkBlock"` block number of the latest fork that should be checked
  + `"forkCanonHash"` hash of the canonical block at `forkBlock`
  + `"bombDefuseTransition"` block number at which the difficulty bomb (epsilon in Yellow Paper Eqs. 39, 44) is removed from the difficulty evolution
  + `"wasmActivationTransition"` block number at which bytecode (in storage or transactions) can be run as Wasm bytecode and by WebAssembly VM.
  + `"maximumUncleCountTransition"` Block at which maximum uncle count parameter should be considered. Default "0" for PoA chains, not set for Ethash chains.
  + `"maximumUncleCount"` Maximum number of accepted uncles starting from the block specified at `maximumUncleCountTransition`. Default `2` for Ethash chains, `0` for PoA chains before `maximumUncleCountTransition`.
  + `"emptyStepsTransition"` Block at which empty step messages should start.
  + `"maximumEmptySteps"` Maximum number of accepted empty steps.
  + `"blockRewardContractAddress"` Address of the smart contract used for calculating block rewards. Use for [Block Reward Contract](Block-Reward-Contract).
  + `"blockRewardContractTransition"` Block at which the block reward contract should start being used.
  + `"nodePermissionContract"` Address of the smart contract that sets nodes' interconnection permissions. Used for [advanced permssioning Networks](Permissioning#how-it-works).
  + `"transactionPermissionContract"` Address of the smart contract that sets the transaction type permissions for network participants. Used for [Advanced permissioning Networks](Permissioning#transaction-type).
  + `"transactionPermissionContractTransition"` Block at which the transaction permission contract should start being used (Default: 0 if not set).
  + `"maxTransactionSize"` Maximum size of a transaction RLP payload. (Default `300*1024`).
  + `"eip150Transition"` [EIP150](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-150.md) transition block number.
  + `"eip160Transition"` [EIP160](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-160.md) transition block number.
  + `"eip161abcTransition"` [EIP161 a,b,c](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-161.md#specification) transition block number.
  + `"eip161dTransition"` [EIP161 d](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-161.md#specification) transition block number.
  + `"eip98Transition"` [EIP98](https://github.com/ethereum/EIPs/issues/98) transition block number.
  + `"eip658Transition"` [EIP658](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-658.md) transition block number introducing the `status` field in transactions' receipts.
  + `"eip155Transition"` - Optional, will be included for block 0 by default - [EIP155](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md) transition block number.
  + `validateReceiptsTransition` - Optional, will be included for block 0 by default - Transition block before which the state root in transaction's receipt can be stripped. 
  + `validateChainIdTransition` - Optional, will be included for block 0 by default - Block before which any `chain_id` in the signature of a replay-protected transaction is accepted. After this transition block, the transactions' `chain_id` must match with the spec `chain_id` to be considered valid.
  + `"eip140Transition"` [EIP-140](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-140.md) (Metropolis: REVERT opcode) transition block number.
  + `"eip210Transition"` [EIP-210](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-210.md) (Metropolis: BLOCKHASH changes) transition block number.
  + `"eip210ContractAddress"` [EIP-210](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-210.md) Blockhash contract address.
  + `"eip210ContractCode"` - Bytes - [EIP-210](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-210.md) Blockhash contract code.
  + `"eip210ContractGas"` - U256 - Gas allocated for EIP-210 blockhash update.
  + `"eip211Transition"` Gas allocated for [EIP-210](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-210.md) blockhash update transition block number.
  + `"eip214Transition"` [EIP214](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-214.md) transition block number.
  + `"eip145Transition"` (Constantinople) [EIP145](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-145.md) transition block number. 
  + `"eip1014Transition"` (Constantinople) [EIP1014](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1014.md) transition block number.
  + `"eip1052Transition"` (Constantinople) [EIP1052](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1052.md) transition block number.
  + `"dustProtectionTransition"` - Activates Mainnet specific [EIP-168](https://github.com/ethereum/EIPs/issues/168) and [EIP169](https://github.com/ethereum/EIPs/issues/168) - Not active by default.
  + `"nonceCapIncrement"` - U64 - Nonce cap increase per block. Nonce cap is only checked if dust protection is enabled..
  + `"removeDustContracts"` - bool - Enable dust cleanup for contracts.
  + `"gasLimitBoundDivisor"` U256 - How much the block gas limit can change between blocks. Miners can vote to bring the block gas limit up or down (via the flag [`--gas-floor-target`](Configuring-Parity-Ethereum)), the new gas limit is callculated according to the formula: `current_gas_limit*(1 ± 1/gasLimitBoundDivisor)`.
  + `"registrar"` Address of the [registrar contract](https://wiki.parity.io/Parity-name-registry.html) which enables the client to locate system contracts like [Automatic Updating](https://wiki.parity.io/Automatic-Updating).
  + `"maxCodeSize"` - U64 - Maximum contract code size that can be deployed in a transaction. Introduced in [EIP170](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-170.md) to mitigate a vulnerability in Ethereum.
  + `"maxCodeSizeTransition"` transition block number for `maxCodeSize` .

[ + "eip86Transition" EIP-86 (Metropolis) transition block number. Commented out before paritytech/parity-ethereum#9140 is merged.]: # ()


+ **`"accounts"`** contains optional contents of the genesis block, such as simple accounts with balances or contracts. Parity does not include the standard Ethereum builtin contracts by default. These are necessary when writing new contracts in Solidity since compiled Solidity often refers to them. To make the chain behave like the public Ethereum chain the 4 contracts need to be included in the spec file, as shown in the example below:

  ```
  "accounts": {
      "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name":   "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
      "0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
      "0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
      "0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } }
  }
  ```

  Other types of accounts that can be specified:
  - simple accounts with some balance `"0x...": { "balance": "100000000000" }`
  - full account state `"0x...": { "balance": "100000000000", "nonce": "0", "code": "0x...", "storage": { "0": "0x...", ... } }`
  - contract constructor, similar to sending a transaction with bytecode `"0x...": { "balance": "100000000000", "constructor": "0x..." }`. The constructor bytecode is executed when the genesis is created and the code returned by the "constructor" is stored in the genesis state.

Optional spec fields:

+ **`"dataDir"`** sets a name of the chain to be used in the data directory instead of the chain name. This field is required if the chain name contains spaces.

+ **`"nodes"`** a list of boot nodes in the [enode format](https://github.com/ethereum/wiki/wiki/enode-url-format)

+ **`"hardcodedSync"`**  Set of information required for light clients to start syncing from a hardcoded block. Can be generated using [`export-hardcoded-sync`](https://wiki.parity.io/FAQ.html#how-to-generate-a-new-hardcoded-sync-block-for-parity-ethereum-light-client) CLI subcommand once the light client is synced.
  + `"header"` header of the block the light clients will start syncing from.
  + `"totalDifficulty"` total difficulty of this block.
  + `"CHTs"` Array of merkle tree root hashes of groups of 2048 blocks.

Example :
```json
"hardcodedSync": {
		"header": "f90219a061d694007fbaca6e23e73e29c8c6a60099abc740ab7e27ae3cd1b9c6a47efef7a01dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347945a0b54d5dc17e0aadc383d2db43b0a0d3e029c4ca0a2f1bdabc1a72737de1c22a76cacc8fc162366904f759a99db7d6d19efee3090a0ac5f5b236e8977928a2ce43c7569ea5a74919643cb0b06d7540407b1ea1298f0a04356ddc5d77c83923a6541260308be167386e0969a608a017770c9e38091cfcab90100a00010002001009080011010141088000004000080081100000a002023000002204204801204084000c000010008000000000880080020c0000440200460000290005010c01c80800080004800100406003380000400402040000028084002a80087000008090a00200100544020019580022000000306100a0080100084020006809000e80000010000254810002000000a240050014200002002c10809202030006422022000203012000241089300080400000009001021020200012410348500028290230408100302000000058c0000020c08c20480081040020260004008481000080000800010010060020000e00020002140100a8988000004400201870b9af4a66df8038350a8018379d54483799eba845ab0426d984554482e45544846414e532e4f52472d3231323134313232a05eeccc80302d8fecca48a47be03654b5a41b5e5f296f271f910ebae631124f518890074810024c6c2b",
		"totalDifficulty": "3144406426008470895785",
		"CHTs": [
			"0x0eb474b7721727204978e92e27d31cddff56471911e424a4c8271c35f9c982cc",
			"0xe10e94515fb5ffb7ffa9bf50db4a959b3f50c2ff75e0b8bd5f5e038749e52a11",
			"0x816e7463af7b5d2fcb804ba55f09e8452182b0ba6c995a34e144245d76333d55",
			"0x3793af64c1ddc07ab61b2ba120034d91c02183ff788f07d3120fd4e6a48305b5",
			"0x14c6106a17e041032210bfa0ca80d11860a1c6d95175d55eff39f97b8d8acded",
			"0x396f832bfa3a9c494e9245471f0e65552613d87b6fe62128103590d95de72c2d",
...
			"0xb060979f095c170a776b2b50a1e2ab0ffea80f6e522753fa36ad6f106ee32e9f",
			"0x8f452e7cbd8a333ed04d819a143a8d3a75fe8c58418e7fc420bb2a717c0d4d2f",
			"0x37fe1b0cf156bfc07571569af210540be753777903a308d5707538fffed75b59",
			"0x6f0561d017cfc123b3f0d37b044e4f7231516b8731a1cab89afb569238643c33",
		]
}
```

## Coming from Geth

To connect to a Geth node or just use the same network setup you can use [the genesis converter](https://github.com/keorn/parity-spec) to generate a Parity compatible chain specification file or the preconfigured [cross-client chain specifications](https://github.com/5chdn/crossclient-chainspec) to get started quickly. The chain spec can be then used by supplying it to the `--chain` Parity option.

To replicate some of the more obscure bugs from Geth's RPC, `--geth` option can be used; be warned, this disables some of the more advanced Parity features so only use it if you know you have to.
