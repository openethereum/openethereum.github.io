## oo7-parity.js Reference

### Chain Inspection
- `findBlock(label: String | number: Number | hash: String) => Header`
- `blocks[label: String | number: Number | hash: String] => Header`
- `current => Header`
- `blockTransactionCount(hash | number) => Number`
- `uncleCount(hash | number) => Number`
- `uncle(hash: String | number: Number, index: Number) => Header`
- `transaction(blockHash: String | number: Number, index: Number) => Transaction`
- `transaction(txHash: String) => Transaction`
- `receipt(txHash: String) => Receipt`

### Accounts Inspection
- `coinbase => `
- `accounts => `
- `me => `
- `accountsInfo() => ` [parity]
- `hardwareAccountsInfo() => ` [parity]

### Signer
- `post() => ` [parity]
- `sign() => ` [parity]
- `deployContract() => ` [parity]
- `makeContract() => ` [parity]

### State Inspection
- `balance() => `
- `code() => `
- `nonce() => `
- `storageAt() => `

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
- `peerCount() => `
- `listening() => `

### parity_
- `hashContent() => ` [parity]
- `gasPriceHistogram() => ` [parity]
- `mode() => ` [parity]

### ...authoring
- `defaultExtraData() => ` [parity]
- `extraData() => ` [parity]
- `gasCeilTarget() => ` [parity]
- `gasFloorTarget() => ` [parity]
- `minGasPrice() => ` [parity]
- `transactionsLimit() => ` [parity]

### ...networking
- `peers() => ` [parity]
- `enode() => ` [parity]
- `nodePort() => ` [parity]
- `nodeName() => ` [parity]
- `signerPort() => ` [parity]
- `dappsPort() => ` [parity]
- `dappsInterface() => ` [parity]

### ...transaction queue
- `nextNonce() => ` [parity]
- `pending() => ` [parity]
- `local() => ` [parity]
- `future() => ` [parity]
- `pendingStats() => ` [parity]
- `unsignedCount() => ` [parity]

### ...auto-update
- `releasesInfo() => ` [parity]
- `versionInfo() => ` [parity]
- `consensusCapability() => ` [parity]
- `upgradeReady() => ` [parity]

### built-in contracts
- `registry() => ` [parity]
- `githubhint() => ` [parity]
- `operations() => ` [parity]
