---
title: Chain Specification
---

By default, when simply running `openethereum`, OpenEthereum will connect to the official public Ethereum network.

In order to run a chain different to the official public Ethereum one, OpenEthereum has to run with the `--chain` option or with a [config file](Configuring-OpenEthereum#config-file) specifying `chain = "path"` under `[parity]`. There are a few named presets that can be selected from or a custom JSON spec file can be supplied.

## Chain presets available
- [`foundation`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/foundation.json) (default) main Ethereum network
- [`goerli`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/goerli.json) and [`rinkeby`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/rinkeby.json) the fast Ethereum test networks using Clique consensus engine.
- [`kovan`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/kovan.json) the [fast Ethereum test network](https://github.com/kovan-testnet/config) using PoA consensus engine.
- [`ropsten`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/ropsten.json) the old Ethereum test network
- [`classic`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/classic.json) Ethereum Classic network
- [`classic-testnet`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/morden.json) original Morden testnet and current Ethereum Classic testnet
- [`expanse`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/expanse.json) Expanse network
- [`dev`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/instant_seal.json) a [Private development chain](Private-development-chain) to be used locally, submitted transactions are inserted into blocks instantly without the need to mine
- [`musicoin`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/musicoin.json) Musicoin network
- [`ellaism`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/ellaism.json) Ellaism network
- [`tobalaba`](https://github.com/openethereum/openethereum/blob/master/ethcore/res/ethereum/tobalaba.json) EWF Tobalaba network

## Private chains

OpenEthereum supports private chain and private network configuration via [Chain specification](Chain-specification) file provided with `--chain`. In addition to the usual [Proof of Work Chains](Proof-of-Work-Chains), OpenEthereum also includes [Proof of Authority Chains](Proof-of-Authority-Chains) which do not require mining.
More details on the available options can be found on the [Pluggable Consensus](Pluggable-Consensus) page.

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

+ **`"engine"`** field describes the consensus engine used for a particular chain, details are explained in the [Consensus Engines](Pluggable-Consensus) section.

+ **`"genesis"`** contains the genesis block (first block in the chain) header information.
  + `"seal"` is consensus engine specific and is further described in [Consensus Engines](Pluggable-Consensus).
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
  + `"networkID"` DevP2P supports multiple networks, and ID is used to uniquely identify each one which is used to connect to correct peers and to prevent transaction replay across chains. See [here](https://ethereum.stackexchange.com/questions/17051/how-to-select-a-network-id-or-is-there-a-list-of-network-ids/17101#17101) for network IDs that are not available anymore.
  + `"maximumExtraDataSize"` integer specifying the maximum size in bytes of the extra_data field of the header. This must be 32 bytes or fewer as per the Ethereum Yellow Paper (see extraData under 4.3). It is an optional value, used by some Ethereum mining pools to log their blocks. The current value for Ethereum and Kovan is "32".
  + `"minGasLimit"` the minimum gas limit.
  
  Optional:
  + `"accountStartNonce"` in the past this was used for transaction replay protection
  + `"chainID"` chain identifier, was introduced in [EIP-155](https://eips.ethereum.org/EIPS/eip-155) to prevent replay attacks between the main ETH and ETC chains, which both have a `networkID` of `1`. `ChainID` is an additional way to tell chains apart. Subsequent to EIP-155, ETH had a `chainID` of 1, while ETC had a `chainID` of `61`. `networkID` and chainID are  required for the chain to operate in general - e.g. it's required when signing transactions. Default value is equal to `networkID`.
  + `"subprotocolName"` Devp2p subprotocol name. Default is `eth`.
  + `"forkBlock"` block number of the latest fork that should be verified. In case of chain split, a node would only warp-sync with a peer whose block hash matches the `forkBlockHash` for a given `forkBlock` to make sure to sync with a given side of a fork.
  + `"forkCanonHash"` hash of the canonical block at `forkBlock`
  + `"bombDefuseTransition"` block number at which the difficulty bomb (epsilon in Yellow Paper Eqs. 39, 44) is removed from the difficulty evolution
  + `"wasmActivationTransition"` block number at which bytecode (in storage or transactions) can be run as Wasm bytecode and by WebAssembly VM.
  + `"nodePermissionContract"` Address of the smart contract that sets nodes' interconnection permissions. Used for [advanced permssioning Networks](Permissioning#how-it-works).
  + `"transactionPermissionContract"` Address of the smart contract that sets the transaction type permissions for network participants. Used for [Advanced permissioning Networks](Permissioning#transaction-type).
  + `"transactionPermissionContractTransition"` Block at which the transaction permission contract should start being used (Default: 0 if not set).
  + `"maxTransactionSize"` Maximum size of a transaction RLP payload. (Default `300*1024`).
  + `"eip150Transition"` [EIP150](https://eips.ethereum.org/EIPS/eip-150) transition block number.
  + `"eip160Transition"` [EIP160](https://eips.ethereum.org/EIPS/eip-160) transition block number.
  + `"eip161abcTransition"` [EIP161 a,b,c](https://eips.ethereum.org/EIPS/eip-161#specification) transition block number.
  + `"eip161dTransition"` [EIP161 d](https://eips.ethereum.org/EIPS/eip-161#specification) transition block number.
  + `"eip98Transition"` [EIP98](https://github.com/ethereum/EIPs/issues/98) transition block number.
  + `"eip658Transition"` [EIP658](https://eips.ethereum.org/EIPS/eip-658) transition block number introducing the `status` field in transactions' receipts.
  + `"eip155Transition"` - Optional, will be included for block 0 by default - [EIP155](https://eips.ethereum.org/EIPS/eip-155) transition block number.
  + `validateReceiptsTransition` - Optional, will be included for block 0 by default - Transition block before which the state root in transaction's receipt can be stripped. 
  + `validateChainIdTransition` - Optional, will be included for block 0 by default - Block before which any `chain_id` in the signature of a replay-protected transaction is accepted. After this transition block, the transactions' `chain_id` must match with the spec `chain_id` to be considered valid.
  + `"eip140Transition"` [EIP-140](https://eips.ethereum.org/EIPS/eip-140) (Metropolis: REVERT opcode) transition block number.
  + `"eip210Transition"` [EIP-210](https://eips.ethereum.org/EIPS/eip-210) (Metropolis: BLOCKHASH changes) transition block number.
  + `"eip210ContractAddress"` [EIP-210](https://eips.ethereum.org/EIPS/eip-210) Blockhash contract address.
  + `"eip210ContractCode"` - Bytes - [EIP-210](https://eips.ethereum.org/EIPS/eip-210) Blockhash contract code.
  + `"eip210ContractGas"` - U256 - Gas allocated for EIP-210 blockhash update.
  + `"eip211Transition"` Gas allocated for [EIP-210](https://eips.ethereum.org/EIPS/eip-210) blockhash update transition block number.
  + `"eip214Transition"` [EIP214](https://eips.ethereum.org/EIPS/eip-214) transition block number.
  + `"eip145Transition"` (Constantinople) [EIP145](https://eips.ethereum.org/EIPS/eip-145) transition block number. 
  + `"eip1014Transition"` (Constantinople) [EIP1014](https://eips.ethereum.org/EIPS/eip-1014) transition block number.
  + `"eip1052Transition"` (Constantinople) [EIP1052](https://eips.ethereum.org/EIPS/eip-1052) transition block number.
  + `"dustProtectionTransition"` - Activates Mainnet specific [EIP-168](https://github.com/ethereum/EIPs/issues/168) and [EIP169](https://github.com/ethereum/EIPs/issues/168) - Not active by default.
  + `"nonceCapIncrement"` - U64 - Nonce cap increase per block. Nonce cap is only checked if dust protection is enabled..
  + `"removeDustContracts"` - bool - Enable dust cleanup for contracts.
  + `"gasLimitBoundDivisor"` U256 - How much the block gas limit can change between blocks. Miners can vote to bring the block gas limit up or down (via the flag [`--gas-floor-target`](Configuring-OpenEthereum)), the new gas limit is callculated according to the formula: `current_gas_limit*(1 ± 1/gasLimitBoundDivisor)`.
  + `"maxCodeSize"` - U64 - Maximum contract code size that can be deployed in a transaction. Introduced in [EIP170](https://eips.ethereum.org/EIPS/eip-170) to mitigate a vulnerability in Ethereum.
  + `"maxCodeSizeTransition"` transition block number for `maxCodeSize` .


+ **`"accounts"`** contains optional contents of the genesis block, such as simple accounts with balances or contracts. OpenEthereum does not include the standard Ethereum builtin contracts by default. These are necessary when writing new contracts in Solidity since compiled Solidity often refers to them. To make the chain behave like the public Ethereum chain the 4 contracts need to be included in the spec file, as shown in the example below:

  ```
  "accounts": {
      "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
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
