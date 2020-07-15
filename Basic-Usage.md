---
title: Basic Usage
---

Once the [syncing](Getting-Synced) is complete, new transactions can be sent. Before relaying a transaction Secure Transaction Signer has to be used to sign it.

- [Configuration](#configuration)
- [Networking](#networking)
- [JSON-RPC API](#json-rpc-api)
- [JavaScript Console](#javascript-console)
    - [Node.js CLI Console](#nodejs-cli-console)


### Configuration

Use `openethereum --help` to find out about available options. To avoid adding options on every run of OpenEthereum a [config file](Configuring-OpenEthereum#config-file).

### Networking

You can connect to the Görli testnet with `openethereum --chain goerli`.

You can override the normal boot nodes with `--bootnodes`, i.e., you might run a local bootnode and sync from that by running:

```bash
$ openethereum --bootnodes enode://YOU_BOOT_NODE_ID_HERE@127.0.0.1:30303
```

To maintain permanent connection to your own set of nodes, you can wire them with the `--reserved-peers` feature. Simply place all node addresses you want to connect to (`enode://...`, one per line) into a text file, e.g., `reserved.txt`, and run OpenEthereum with:

```bash
$ openethereum --reserved-peers /path/to/reserved.txt
```

### JSON-RPC API

Assuming you start with JSONRPC API enabled (default), then [Ethereum's JSON-RPC](JSONRPC) can be used, e.g.:

```bash
$ curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x0037a6b811ffeb6e072da21179d11b1406371c63", "latest"],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","result":"0x0406c5a45045137507eb","id":1}
```

### JavaScript Console

Ethereum has the [web3 Javascript API](https://github.com/ethereum/wiki/wiki/JavaScript-API) for interacting with an Ethereum client.

##### Node.js CLI Console

OpenEthereum doesn't include a JavaScript interpreter but if you want to use an interactive JavaScript console, you can install [node and npm](http://nodejs.org) and use its console. Once you have node/NPM installed, you'll just need to install the latest Web3 module:

```bash
$ npm install web3
```

From then on you just need to run `node` and require the Web3 module:

```javascript
$ node
> Web3 = require("web3")
> web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
```

After this point, you'll be able to use the Web3 API from with this environment, e.g.:

```javascript
> web3.eth.blockNumber
743397
```
