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
- `consensusCapability => Label` [parity]
- `upgradeReady => bool` [parity_set]

### Basic Contracts
- `registry => Contract` [parity]
- `githubhint => Contract` [parity]
- `operations => Contract` [parity]
