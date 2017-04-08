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

### `parity.bonds` API Collection

#### Notes on Usage

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

#### Chain Inspection
- `height => Number`: The height of the chain.
- `findBlock(Number | Hash) => Block`: The block identified by the `Number` or `Hash`.
- `blocks[Number | Hash] => Block`: The block identified by the `Number` or `Hash`.
- `head => Block`: The most recently authored block on the chain.
- `blockTransactionCount(Hash | Number) => Number`: The number of transactions in the block identified.
- `uncleCount(Hash | Number) => Number`: The number of uncle headers in the block identified.
- `uncle(blockHash: Hash | blockNumber: Number, uncleIndex: Number) => Header`: The uncle of index `uncleIndex` in the block identified by `blockHash` or `blockNumber`.
- `transaction(blockHash: Hash | blockNumber: Number, txIndex: Number) => Transaction`: The transaction of index `txIndex` in the block identified by `blockHash` or `blockNumber`.
- `transaction(txHash: Hash) => Transaction`: The transaction (which must appear in a block) whose hash is `txHash`.
- `receipt(txHash: Hash) => Receipt`: The receipt of the transaction (which must appear in a block) whose hash is `txHash`.

#### Accounts Inspection
- `me => Address`: The address of the account which the user would like the Dapp to act under.
- `accounts => [Address]`: All addresses of accounts which the user would like the Dapp to know about.
- `accountsInfo => { Address -> AccountInfo }` [parity]: Information on all accounts which the user would like the Dapp to know about.
- `hardwareAccountsInfo => { Address -> AccountInfo }` [parity]: Information on all hardware (e.g. Ledger) accounts which the user would like the Dapp to know about.

#### Signer
- `post(Transaction) => TransactionStatus` [parity]: Posts the described transaction to the node for signing and publication. Evaluates to the status of the transaction as it becomes finalised (or not). Note: it is not supported to post a transaction which fails with an exception and unless `gas` is supplied then the lowest amount of gas will be used.
- `sign(Address, Bytes) => SignatureStatus` [parity]: Issues a request to the user to sign a message, prepended with a known string for security. Evaluates to the status of the request.
- `deployContract(initCode: Bytes, ABISpec, Transaction?) => DeploymentStatus` [parity]: Attempts to deploy a contract of the given initialisation code. Evaluates to the status of the request, including a contract object on finalisation. 
- `makeContract(Address, ABISpec) => Contract` [parity]: Creates a new contract object with the given `Address` and ABI specification.

#### State Inspection
- `balance(Address) => BigNumber`: The balance of the account with `Address`.
- `code(Address) => Bytes`: The code of the account with `Address`, if any.
- `nonce(Address) => BigNumber`: The nonce of the account with `Address`.
- `storageAt(Address, Location: BigNumber) => BigNumber`: The contents of the storage `Location` of the account with `Address`.
- `call(Transaction) => Bytes`: The output of the `Transaction` when executed.
- `estimateGas(Transaction) => BigNumber`: The lowest amount of gas that the `Transaction` will execute with before it becomes invalid or fails with an exception.

#### Node Status
- `syncing => bool`: True when the node's state is currently out of date. False when it might be.
- `authoring => bool`: Is the node currently authoring blocks?
- `gasPrice => BigNumber`: The default gas price used by the node when posting transactions.
- `gasPriceHistogram => [BigNumber]` [parity]: The histogram of gas prices of recent finalised transactions.
- `mode => Label` [parity]: The current networking mode of Parity; one of `off`, `dark`, `passive`, `active`.

#### Version Inspection
- `clientVersion => String`: The string describing the version of the client.
- `ethProtocolVersion => Number`: The `eth` protocol version number.
- `versionInfo => VersionInfo` [parity]: Extended information on the version of the client.

#### Chain Metadata
- `chainId => Number` [parity] The identity of the chain e.g. `42`.
- `chainName => String` [parity] The name of the chain e.g. `kovan`.
- `chainStatus => ChainStatus` [parity]: Information concerning the status of the local replication of the blockchain.

#### P2P Network
- `peerCount => Number`: How many other nodes on the p2p network we are connected to.
- `listening => bool`: Are we allowing inbound connections from other nodes?
- `peers => Peers` [parity]: Information on each node to which we are connected.
- `enode => String` [parity]: The node's "enode" (network ID). 
- `nodePort => Number` [parity]: The port on which the node listens for incoming p2p connections.
- `nodeName => String` [parity]: The name give to peers to identify this node in peer lists.

#### Block Authoring (aka "mining")
- `author => Address`: The address of the author of blocks.
- `defaultExtraData => Bytes` [parity]: The default for authored blocks' "extra data" value (a client ID).
- `extraData => Bytes` [parity]: The value used for authored blocks' "extra data".
- `gasCeilTarget => BigNumber` [parity]: The maximum gas limit to be targeted - it will be reduced in blocks we author as much as possible if it is above this value.
- `gasFloorTarget => BigNumber` [parity]: The minimum gas limit to be targeted - it will be increase in blocks we author as much as possible if it is below this value.
- `minGasPrice => BigNumber` [parity]: The minimum gas price for which we will accept transactions.
- `transactionsLimit => Number` [parity]: The maximum amount of transactions we will place in a block.

#### Other Services
- `signerPort => Number` [parity] The port on which the (trusted) signer RPC server is currently listening.
- `dappsPort => Number` [parity] The port on which the (untrusted) dapps RPC server is currently listening.
- `dappsInterface => String` [parity] The interface on which the dapps RPC server is currently listening.
- `hashContent(url: String) => Hash` [parity_set]: The Keccak hash of the content at the given URL, at the time of the call (this does not react to any changes in the content at `url` but will react should the address `url` itself change).

#### Transaction Queue
- `nextNonce(Address) => Number` [parity]: The next valid nonce of `Address`. Includes pending and future transactions as well as the state of the head of the chain.
- `pending => [Transaction]` [parity]: The (signed) transactions currently pending finalisation in the queue.
- `local => { Hash -> LocalTransaction }` [parity]: Locally-submitted transactions, including those both finalised and pending. 
- `future => [Transaction]` [parity]: Queued transactions whose nonce is too high such that they're not yet valid candidates for finalisation.
- `pendingStats => { Hash -> PendingTransaction }` [parity]: Extended information concerning a particular (identified) transaction pending finalisation.
- `unsignedCount => Number` [parity]: The number of unsigned transactions pending signing.

#### Consensus & Updates
- `releasesInfo => ReleaseInfo` [parity]: Information on the latest releases of Parity.
- `consensusCapability => Capability` [parity]: Information on the capability of taking part in the network's consensus of the current node. One of `'unknown'`, `'capable'`, `{"capableUntil": BlockNumber}`, `{"incapableSince": BlockNumber}`.
- `upgradeReady => bool` [parity_set]: Whether there is currently an upgrade which is ready to install & execute.

#### Basic Contracts
- `registry => Contract` [parity]: The root Parity name registry contract.
- `githubhint => Contract` [parity]: The primary URL hinting contract.
- `operations => Contract` [parity]: The primary operations contract.

## Basic Types

- `bool`: A Javascript `bool`. May be `true` or `false`.
- `Number`: A number. May be passed in one of two forms:
   - Basic Javascript `Number` type, e.g. `69`. This is the default when being returned.
   - A Javascript `String` containing a decimal number, e.g. `"42"`.
- `BigNumber`: A large number, up to `2**256 - 1`. May be passed in one of three forms:
   - A `BigNumber` object. This is the default when being returned.
   - Basic Javascript `Number` type.
   - A Javascript `String` containing a decimal number.
- `String`: A freeform Javascript `String`.
- `Hash`: A string beginning with `0x` and with 64 hex digits following. Represents a 32-byte piece of data.
- `Address`: A string beginning with `0x` and with 40 hex digits following. Represents a 20-byte account address.
- `Bytes`: A string beginning with `0x` and with an even number of hash digits following. Represents an arbitrary length series of bytes.
- `Label`: A string containing one of a number of alternative words/phrases.
- `[Type]`: An `Array` of `Type`. The responsible `Bond` can usually be sub-scripted by an index in order to provide a `Bond` evaluating to that item.

## Object Types

Objects going from one type to another are denoted `{ KeyType ->  ValueType }`. Others are listed here.

### `Block`

Equivalent to "a block object", described at [`eth_getBlockByHash` RPC](https://github.com/paritytech/parity/wiki/JSONRPC-eth-module#eth_getblockbyhash).

### `Header`

Equivalent to `Block`, except that individual transactions are not provided.

### `Transaction`

Equivalent to "a transaction object", described at [`eth_getTransactionByHash` RPC](https://github.com/paritytech/parity/wiki/JSONRPC-eth-module#eth_gettransactionbyhash).

### `Receipt`

Equivalent to "a transaction receipt object", described at [`eth_getTransactionReceipt` RPC](https://github.com/paritytech/parity/wiki/JSONRPC-eth-module#eth_gettransactionreceipt).

### `AccountInfo`

Information describing a controlled account.

#### Keys

- `name`: The name given to the account by the user.

Example:

```json
{
  "name": "Main Account"
}
```

### `TransactionStatus`

The current status of a transaction to be committed to the blockchain. Must contain exactly one of the following keys.

#### Keys

- `estimating`: Always `null`. Implies that the minimum required gas for the transaction is being estimated.
- `estimated`: The computed gas required for the transaction to succeed. Implies the user is about to be requested to approve the transaction.
- `requested`: An internal identifier. Implies the user has been requested to approve this transaction.
- `signed`: A signed transaction given as `Bytes`. Implies the transaction is about to be published to the network for inclusion in a block.
- `confirmed`: The transaction's receipt. Implies that the transaction has been finalised.
- `failed`: An error. Implies that the operation met with a failure at some point. Usually due to either the user rejecting approval of the transaction or that no amount of gas could be found for successful transaction execution.

Example:

```json
{
   "estimating": null
}
```

### `SignatureStatus`

The current status of a message to be signed. Must contain exactly one of the following keys.

#### Keys

- `requested`: An internal identifier. Implies the user has been requested to approve this transaction.
- `signed`: A signed transaction given as `Bytes`. Implies the transaction is about to be published to the network for inclusion in a block.
- `failed`: An error. Implies that the operation met with a failure at some point. Usually due to either the user rejecting approval of the transaction or that no amount of gas could be found for successful transaction execution.

Example:

```json
{
   "signed": "0x1b0749bc94188f3d06b00217fe384a8aced3a9f6b4f21e22a4eed73e4e3f036a4913b439ed3fab0985353e9f0c9d748d42f6c1786726c6ab530c1f66772c6e5d63"
}
```

### `ABISpec`

An ABI specification, as described in the [Ethereum wiki article](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).

### `ChainStatus`

Information on the status of the portion of the chain maintained locally. Equivalent to "the status object" described for the [`parity_chainStatus` RPC](https://github.com/paritytech/parity/wiki/JSONRPC-parity-module#parity_chainstatus).

### `Peers`

Information on the peers known by the node.

#### Keys

- `active`: Number of peers in active communication with the node.
- `connected`: Number of nodes connected as peers.
- `max`: Maximum acceptable number of peers.
- `peers`: Details of each peer expressed as an `Array`. Each item is an object with keys:
  - `caps`: An `Array` of versioned capability identifiers given as `"<subprotocol-identifier>/<version>"`.
  - `id`: The _enode_ identifier.
  - `name`: The node's full identifier.
  - `network`: An object with two keys:
    - `localAddress`: Locally assigned IP address and port.
    - `remoteAddress`: Remotely assigned IP address and port used for incoming connections.
  - `protocols`: An `Object` describing the state of the node in terms of each sub-protocol it supports. Maps sub protocol identifier to an `Object` with subprotocol-specific keys.

Example:

```json
{
  "active": "0",
  "connected": "1",
  "max": "25",
  "peers": [
    {
      "caps": [
        "eth/62",
        "eth/63",
        "par/1",
        "par/2"
      ],
      "id": "2aa81bd0a761cd4f02c934dcf3f81c5b65953e51ab5ba03ceb1f125eb06418a1cdffb1c9d01871aa7bd456f3fce35e745608189ad1164f72b2161634b0c3f6ea",
      "name": "Parity/v1.6.5-beta-987390f-20170328/x86_64-linux-gnu/rustc1.16.0",
      "network": {
        "localAddress": "192.168.0.15:63340",
        "remoteAddress": "188.166.240.190:30303"
      },
      "protocols": {
        "eth": {
          "difficulty": "2.03152996722570234887659664506662254654900182e+44",
          "head": "daac19a336359bc4a46b06bfc47e9aa62f5cde2974c987a70213fa2431a8d9d5",
          "version": 2
        }
      }
    }
  ]
}
```

### `LocalTransaction`

Information concerning a locally-submitted transaction. An object which maps 

#### Keys

- `status`: Information regarding the status of this transaction. Must be one of:
  - `"pending"`: Transaction is pending finalisation.
  - `"future"`: Transaction's nonce is not yet valid.
  - `"mined"`: Transaction has been mined.
  - `"dropped"`: Transaction was dropped because of queue's limit.
  - `"replaced"`: Transaction was replaced by another with higher gas price.
  - `"rejected"`: Transaction was rejected prior to entry into the queue.
  - `"invalid"`: Transaction is invalid.
  - `"canceled"`: Transaction was canceled.

Example:

```json
{
  "status": "mined",
  "transaction": /* ellided */
}
```

### `PendingTransaction`

### `ReleaseInfo`

Information concerning available updates.

#### Keys

- `fork`: The blockchain's most recent fork identifier (generally the transition block number of the last approved fork).
- `minor`: Information on the latest release shared by the node's minor version. [experimental!]
- `this_fork`: The highest fork identifier supported by the currently executing node.
- `track`: Information on the latest release on the node's release track.

#### Example

```json
{
  "fork": 0,
  "this_fork": null,
  "track": new ReleaseVersion()
}
```

### `VersionInfo`

Description of a release of Parity.

#### Keys

- `binary`: The Keccak hash of the release's executable on the node's platform.
- `fork`: The latest fork identifier supported by this version.
- `is_critical`: Whether this release contains critical (e.g. security) fixes.
- `version`: Information on the releases version identifier.
  - `hash`: Git commit hash of the the release.
  - `track`: Release track on which this version was made (one of `'stable'`, `'beta'`, `'nightly'` or `'testing'`).
  - `version`: Semantic version of the release.
    - `major`: Major component.
    - `minor`: Minor component.
    - `patch`: Patch component.

#### Example

```json
{
  "binary": "0xf4e506d60763739ff9d59dfac577c71a3791512d7c552f8aa209d4069c11a9db",
  "fork": 0,
  "is_critical": false,
  "version": {
    "hash": "0x987390fb7d9f42a97c34aba254a8b9d8efd461d7",
    "track": "beta",
    "version": {
      "major": 1,
      "minor": 6,
      "patch": 5
    }
  }
}
```

### `Contract`

An object representing a (deployed) instance of a contract. Contains one basic field and then additional functions for each of three types of items within the contract: `public` fields and `constant` functions; other (state-mutating) `public` functions and events.

#### Basic Keys

- `address`: The address of this contract; may be a `Bond`.

#### Fields and `constant` functions

These are the simplest to use. Each such item is represented as a function. This takes arguments in the case of a parameterised `constant` function or when the field represents an array, struct or mapping, correlated accordingly. Each parameter may itself be a `Bond`.


When the function is invoked, a `Bond` is returned expressing the ongoing value that the contract gives when queried. This is reactive to all changes, including those in the contract's state, on its `address` (should that be a `Bond`) and on its parameters (where they are `Bond`s).

Example:

```js
// Prints `gavofyork`'s address to the console.
parity.bonds.registry.getAddress(parity.util.sha3('gavofyork'), 'A')
   .then(console.log)
```

#### State-mutating functions

Each such item is represented as a function. This takes arguments exactly correlating to the function it maps from. When the function is invoked, a `TransactionStatus` bond is returned, tracking the state of the transaction required to execute and finalise that function.

The parameters of these functions may be `Bond`s, but the resultant `Bond` is not reactive to changes in parameters since they are one-off commands only.

Example:

```js
// Prints a message depending on whether we managed to change the address of `gavofyork`.
let b = parity.bonds.registry.setAddress(parity.util.sha3('gavofyork'), 'A', parity.bonds.me);
b.tie(s => {
   if (s.failed) { console.log(`setAddress failed: ${s.failed}`); }
   else if (s.confirmed) { console.log(`setAddress completed at #${s.confirmed.blockNumber}`); }
   else { return; }
   b.untie();
})
```

#### Events

Events are represented as functions providing a `Bond` representing the state of a particular query. The query is described with two parameters passed to the function; both are given as objects. The value of the `Bond` is simply  an array of (matching) events emitted by the contract. The first describes a filter applied over any or all of the `indexed` event arguments, allowing the resultant `Bond` to contain only events   that match this filter. The second allows the provision of further options to govern which, and a limit on how many, events are contained in the `Bond`'s value.

##### Result value

This is an array of objects describing specific events emitted by the contract. Keys include:

- `event`: The name of the `event` whose instance is described.
- `log`: The underlying raw log item, as specified and returned by `eth_getFilterLogs`.

In addition to these keys, further keys are provided for each event argument, named according to the corresponding argument in the original `event`.

##### First parameter: `filter`

This is the first of two parameters and helps filter events in the resultant array. `filter` is an object where each key must be the name of an event parameter marked `indexed`. The value may be either a required value for that parameter to be, or an array of alternative values.

All keys stated must be fulfilled for an event to be returned.

##### Second parameter: `options`

This is the second of two parameters. `options` is an object with three keys:

- `fromBlock`: The block number before which no events shall be returned. Defaults to `0` if `undefined` or `null`.
- `toBlock`: The block number after which no events shall be returned. Defaults to `pending` if `undefined` or `null`.
- `limit`: The maximum amount of events to return. Defaults to `10` if `undefined` or `null`.