## oo7-parity.js Reference

### Notes on Usage

For brevity we have dropped the prefix of `parity.bonds.`.

All arguments may be given as one of:
- a plain value, e.g. `parity.bonds.findBlock(69)`;
- a `Bond` object, e.g. `parity.bonds.findBlock(parity.bonds.blockNumber)`;
- an `Object` or `Array` containing a mixture of `Bond`s and plain values e.g. `parity.bonds.send({to: parity.bonds.author, value: '1000000000000000000'})`.

Note that items with no trailing parens should not be used as functions - they are `Bond`s in their own right e.g. this is right: `parity.bonds.current.then(console.log)`; `...current().then...` is **wrong**.

The type after `=>` denotes the value type of `Bond` returned.

If a function may take multiple types of parameters, then each type is separated with a pipe `|`.

If a function's parameter is optional, then a `?` immediately follows it.

If an item only works when the node is Parity, then the item's prototype is followed with "[parity]".

If the parameter of a function's usage is non-obvious, then a `:` is appended, followed by an informative name.

### Chain Inspection
- `height => Number`
- `findBlock(Label | Number | Hash) => Header`
- `blocks[Label | Number | Hash] => Header`
- `current => Header`
- `blockTransactionCount(Hash | Number) => Number`
- `uncleCount(Hash | Number) => Number`
- `uncle(blockHash: Hash | blockNumber: Number, uncleIndex: Number) => Header`
- `transaction(blockHash: Hash | blockNumber: Number, txIndex: Number) => Transaction`
- `transaction(txHash: Hash) => Transaction`
- `receipt(txHash: Hash) => Receipt`

### Accounts Inspection
- `me => Address`
- `accounts => [Address]`
- `accountsInfo => { Address -> AccountInfo }` [parity]
- `hardwareAccountsInfo => { Address -> AccountInfo }` [parity]

### Signer
- `post(Transaction) => TransactionStatus` [parity]
- `sign(Address, Bytes) => SignatureStatus` [parity]
- `deployContract(initCode: Bytes, ABISpec, Transaction?) => DeploymentStatus` [parity]
- `makeContract(Address, ABISpec) => Contract` [parity]

### State Inspection
- `balance(Address) => BigNumber`
- `code(Address) => Bytes`
- `nonce(Address) => BigNumber`
- `storageAt(Address, Location: BigNumber) => BigNumber`
- `call(Transaction) => Bytes`
- `estimateGas(Transaction) => BigNumber`

### Node Status
- `syncing => bool`
- `hashrate => Number`
- `mining => bool`
- `gasPrice => BigNumber`
- `gasPriceHistogram => [BigNumber]` [parity]
- `hashContent(url: String) => Bytes` [parity]
- `mode => Label` [parity]

### Version Inspection
- `clientVersion => String`
- `protocolVersion => Number`
- `versionInfo => VersionInfo` [parity]

### Chain Metadata
- `chainId => Number` [parity] The identity of the chain e.g. `42`.
- `chainName => ` [parity] The name of the chain e.g. `kovan`.
- `chainStatus => ChainStatus` [parity]

### P2P Network
- `peers => { enode: String -> Peer }` [parity]
- `peerCount => Number`
- `listening => bool`
- `enode => String` [parity]
- `nodePort => Number` [parity]
- `nodeName => String` [parity]

### Block Authoring (aka "mining")
- `author => Address`
- `defaultExtraData => Bytes` [parity]
- `extraData => Bytes` [parity]
- `gasCeilTarget => BigNumber` [parity]
- `gasFloorTarget => BigNumber` [parity]
- `minGasPrice => BigNumber` [parity]
- `transactionsLimit => BigNumber` [parity]

### Other Services
- `signerPort => Number` [parity]
- `dappsPort => Number` [parity]
- `dappsInterface => String` [parity]

### Transaction Queue
- `nextNonce(Address) => Number` [parity]
- `pending => [Transaction]` [parity]
- `local => { Hash -> LocalTransaction }` [parity]
- `future => [Transaction]` [parity]
- `pendingStats => { Hash -> PendingTransaction }` [parity]
- `unsignedCount => Number` [parity]

### Consensus & Updates
- `releasesInfo => ` [parity]
- `consensusCapability => Label` [parity]
- `upgradeReady => bool` [parity_set]

### Basic Contracts
- `registry => Contract` [parity]
- `githubhint => Contract` [parity]
- `operations => Contract` [parity]

## Types

Objects going from one type to another are denoted `{ KeyType = ValueType }`.

TODO
