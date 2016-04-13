# Quick Start

### Docker Environment

Docker's great for making sure differences between operating systems, distributions, installations and build environments don't get in the way of coding fun. For this quick start, we'll just use docker to set up a minimal Ubuntu installation and take it from there. However, you don't strictly need docker - similar instructions can almost certainly be used to get things working on pretty much any Linux installation or a Mac Homebrew system, but don't come complaining if it doesn't work for you on some niche distribution (Arch, I'm looking at you!) with 6 months of cruft caking it up. Rather, get the docker image working and then figure out what the differences are.

*NOTE*: Ensure you have docker to begin with.

```
docker run -it ubuntu bash
```

This will give you a temporary docker environment.

### Grab Rust

This will download and install Rust:

```
curl -sf https://raw.githubusercontent.com/brson/multirust/master/blastoff.sh | sh
```

### Install and Build Parity

Next, grab the parity repository:

```
git clone https://github.com/ethcore/parity && cd parity
```

You can build with:

```
cargo build
```

You can run the unit tests with:

```
./test.sh
```

You can run just the consensus tests with:

```
cargo test --release --features ethcore/json-tests -p ethcore
```

You can start a client and sync with the network with:

```
cargo run --release
```

To get help on the command line options for the `parity` client, use `--help`:

```
cargo run --release -- --help
```

Have fun.

### Quick Start with the one-line installer:

If you are using OS X or Ubuntu you can try the one-line installer script:

```
bash <(curl https://raw.githubusercontent.com/ethcore/parity/master/get-deps.sh -L)
```

This will install and configure the Parity client for you. 

On Ubuntu this script will also offer to install the [Netstats client](https://github.com/cubedro/eth-net-intelligence-api) and connect it to [stats.ethdev.com](https://stats.ethdev.com). The script will prompt you for entering the secret key needed for connecting to the Netstats server. You can learn the key by joining the Netstats [Skype group](http://is.gd/iwSaR9).

### Ubuntu Snappy on Raspberry Pi/ARM

There are Ubuntu Snappy builds for the RasPi, found in [Parity Snappy repository](github.com/ethcore/parity-snappy). TODO: installation instructions, @NikVolf / @GeneralBeck?

### Start the parity Client

Start the client with `parity`. By default it will connect to other nodes on the Ethereum Homestead network and download the blockchain. You can instead connect to the Morden testnet with `parity --chain morden`. By default it will run in archive mode saving all intermediate from previous blocks; to avoid doing this (and freeing up a couple of gigs of disk space) use a pruning option e.g. `parity --pruning fast`.

You can override the normal boot nodes and connect to your own nodes by using `parity --bootnodes ...`, e.g. you might run a local `geth` node and sync from that by running `parity --bootnodes enode://YOU_GETH_NODE_ID_HERE@127.0.0.1:30303`. You need to check geth's output to figure out what node ID is.

Use `parity --help` to get help on other options.

### JSON-RPC API

Assuming you start with JSONRPC API enabled (`parity -j`), then you can do most of the examples on [Ethereum's JSON-RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC):

````
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xYOUR_ADDRESS_HERE", "latest"],"id":1}'
````

### Javascript Console

Ethereum has the [web3 Javascript API](https://github.com/ethereum/wiki/wiki/JavaScript-API) for interacting with an Ethereum client. Now, Parity doesn't include a Javascript interpreter but if you want to use an interactive Jaavscript console, you can install [node/NPM](http://nodejs.org) and use its console. You'll need to have JSONRPC enabled by running Parity with the `-j` flag. Once you have node/NPM installed, you'll just need to install the latest web3 module:

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

### Experimental browser support

In the most recent version of Parity, you can also use the Google Chrome console for web3 development. You'll need to run Parity with the `-jw` flags to enable the web integration interface. With this done, head to [http://localhost:8080/](http://localhost:8080/) and you'll be able to use Chrome's builtin console right away.

