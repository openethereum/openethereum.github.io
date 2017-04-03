## oo7-parity.js Reference

### Chain Inspection
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
- `me => Account`
- `accounts => [Account]`
- `accountsInfo => { Account -> AccountInfo }` [parity]
- `hardwareAccountsInfo => { Account -> AccountInfo }` [parity]

### Signer
- `post(Transaction) => TransactionStatus` [parity]
- `sign(Account, Bytes) => SignatureStatus` [parity]
- `deployContract(initCode: Bytes, ABISpec, Transaction = null) => DeploymentStatus` [parity]
- `makeContract(Address, ABISpec) => Contract` [parity]

### State Inspection
- `balance(Account) => BigNumber`
- `code(Account) => Bytes`
- `nonce(Account) => BigNumber`
- `storageAt(Account, Location: BigNumber) => BigNumber`

### Node Status
- `syncing() => `
- `hashrate() => `
- `mining() => `
- `gasPrice() => `
- `estimateGas() => `

### Version Inspection
- `clientVersion() => `
- `protocolVersion() => `

### Chain Metadata
- `chainId() => ` [parity]
- `chainName() => ` [parity]
- `chainStatus() => ` [parity]

### P2P Network
- `peers => { enode: String -> Peer }` [parity]
- `peerCount => Number`
- `listening => bool`
- `enode => String` [parity]
- `nodePort => Number` [parity]
- `nodeName => String` [parity]

### Miscellaneous
- `hashContent(url: String) => Bytes` [parity]
- `gasPriceHistogram => [BigNumber]` [parity]
- `mode => Label` [parity]

### Block Authoring (aka "mining")
- `author => Account`
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
- `nextNonce(a: Account) => Number` [parity]
- `pending => [Transaction]` [parity]
- `local => { Hash -> LocalTransaction }` [parity]
- `future => [Transaction]` [parity]
- `pendingStats => { Hash -> PendingTransaction }` [parity]
- `unsignedCount => Number` [parity]

### Consensus & Updates
- `releasesInfo => ` [parity]
- `versionInfo => ` [parity]
- `consensusCapability => ` [parity]
- `upgradeReady => ` [parity]

### Basic Contracts
- `registry => Contract` [parity]
- `githubhint => Contract` [parity]
- `operations => Contract` [parity]
