`oo7.js` is a `Bond` library: a `Bond` is like a `Promise` in that it's an asynchronously provided value. However, unlike a `Promise` it autonomously stays up to date. `Bond`s may be dereferenced, transformed and composed. `oo7-react.js` exists for tying `Bond`s into the React library, trivially allowing the UI to display up-to-date information from the block chain.

### Installation and Setup

#### Server-side

`oo7-parity.js` is available on NPM. Components of the underlying library, `oo7.js` are generally helpful to have around:

   `npm install --save oo7 oo7-parity`

#### UI-side

If you are working on the UI with React, you'll probably want to install `oo7-react.js` and `parity-reactive-ui.js`, too:

   `npm install --save oo7 oo7-parity oo7-react parity-reactive-ui`

In the server-side case, you'll want to include the `parity.js` object and then polyfill and import the `oo7-parity.js` API, e.g.:

```js
const Parity = require('@parity/parity.js');
const oo7_parity = require('oo7-parity');
const transport = new Parity.Api.Transport.Http('http://localhost:8545');
let parity = {api: new Parity.Api(transport)};
```

When browsing under a Dapp display environment such as Parity Chrome Extension, the `parity` object will automatically be injected into the `window` object, and the above is not necessary. Under other environments, such as Mist and Metamask, you may need to use the above code.

In both the UI and server-side cases, a polyfill and installation is needed afterward to deposit the `oo7-parity` APIs into the basic `parity` object:

```js
import {setupBonds, abiPolyfill} from 'oo7-parity';
parity.api.abi = abiPolyfill(parity.api);
parity.bonds = setupBonds(parity.api);
```

### Notes on Usage

For brevity we have dropped the prefix of `parity.bonds.`.

All arguments may be given as one of:
- a plain value, e.g. `parity.bonds.findBlock(69)`;
- a `Bond` object, e.g. `parity.bonds.findBlock(parity.bonds.blockNumber)`;
- an `Object` or `Array` containing a mixture of `Bond`s and plain values e.g. `parity.bonds.send({to: parity.bonds.author, value: '1000000000000000000'})`.

Note that items with no trailing parens should not be used as functions - they are `Bond`s in their own right e.g. this is right: `parity.bonds.head.then(console.log)`; `...head().then...` is **wrong**.

The type after `=>` denotes the value type of `Bond` returned.

If a function may take multiple types of parameters, then each type is separated with a pipe `|`.

If a function's parameter is optional, then a `?` immediately follows it.

If an item only works when the Ethereum client is Parity, then the item's prototype is followed with "[parity]".

If the parameter of a function's usage is non-obvious, then a `:` is appended, followed by an informative name.

One item (`blocks`) is denoted an array: it may be dereferenced as an array e.g. `parity.bonds.blocks[parity.bonds.current]`.

Items that return `Array`s or `Object`s may be dereferenced directly, e.g. `parity.bonds.head.author` would be the `Bond` that evaluates to the author of the block currently at the head of the chain.

### Chain Inspection
- `height => Number`: The height of the chain.
- `findBlock(Number | Hash) => Header`: The block identified by the `Number` or `Hash`.
- `blocks[Number | Hash] => Header`: The block identified by the `Number` or `Hash`.
- `head => Header`: The most recently authored block on the chain.
- `blockTransactionCount(Hash | Number) => Number`: The number of transactions in the block identified.
- `uncleCount(Hash | Number) => Number`: The number of uncle headers in the block identified.
- `uncle(blockHash: Hash | blockNumber: Number, uncleIndex: Number) => Header`: The uncle of index `uncleIndex` in the block identified by `blockHash` or `blockNumber`.
- `transaction(blockHash: Hash | blockNumber: Number, txIndex: Number) => Transaction`: The transaction of index `txIndex` in the block identified by `blockHash` or `blockNumber`.
- `transaction(txHash: Hash) => Transaction`: The transaction (which must appear in a block) whose hash is `txHash`.
- `receipt(txHash: Hash) => Receipt`: The receipt of the transaction (which must appear in a block) whose hash is `txHash`.

### Accounts Inspection
- `me => Address`: The address of the account which the user would like the Dapp to act under.
- `accounts => [Address]`: All addresses of accounts which the user would like the Dapp to know about.
- `accountsInfo => { Address -> AccountInfo }` [parity]: Information on all accounts which the user would like the Dapp to know about.
- `hardwareAccountsInfo => { Address -> AccountInfo }` [parity]: Information on all hardware (e.g. Ledger) accounts which the user would like the Dapp to know about.

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
