# Basic Usage

To use Parity simply run `parity` and then head on to [http://localhost:8080/](http://localhost:8080/) or another port specified by `--dapps-port PORT` option. This will bring up an UI that can be used to monitor Ethereum blockchain syncing progress and use Mist wallet or any other dapps. Once the syncing is complete, transactions can be sent. Before relaying a transaction [Secure Transaction Signer](Secure Transaction Signer) has to be used to sign it. This is done by clicking the extension button and following the instructions.

You can also connect to the Morden testnet with `parity --testnet`.

You can override the normal boot nodes and connect to your own nodes by using `parity --bootnodes ...`, e.g. you might run a local `geth` node and sync from that by running `parity --bootnodes enode://YOU_GETH_NODE_ID_HERE@127.0.0.1:30303`. You need to check geth's output to figure out what node ID is.

Use `parity --help` to find out about other available options.

## JSON-RPC API

Assuming you start with JSONRPC API enabled (default), then [Ethereum's JSON-RPC](JSONRPC) can be used. e.g.:

````
> curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x0037a6b811ffeb6e072da21179d11b1406371c63", "latest"],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","result":"0x0406c5a45045137507eb","id":1}
````

## Javascript Console

Ethereum has the [web3 Javascript API](https://github.com/ethereum/wiki/wiki/JavaScript-API) for interacting with an Ethereum client.

### Node.js CLI Console

Parity doesn't include a Javascript interpreter but if you want to use an interactive Jaavscript console, you can install [node/NPM](http://nodejs.org) and use its console. You'll need to have JSONRPC enabled by running Parity with the `-j` flag. Once you have node/NPM installed, you'll just need to install the latest web3 module:

```bash
$ npm install web3
```

From then on you just need to run `node` and require the web3 module:

```bash
$ node
> Web3 = require("web3")
> web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
```

After this point, you'll be able to use the web3 API from with this environment, e.g.:

```javascript
> web3.eth.blockNumber
743397
```
