# Quick Start

There are a few ways of proceeding here. You can build Parity from the sources; you can install Parity from our binary releases for Ubuntu and Mac/Homebrew or, if you're on an Ubuntu Snappy platform, just use our Snappy App. Support for Windows was added in version 1.2, as planned in issue [#462](https://github.com/ethcore/parity/issues/462). Other Unix-like environments should work (assuming you have the latex *nix installed); we're not going to expend much effort supporting them, though build PRs are welcome.

## Quick Start: Building from the CLI

Docker is great for making sure differences between operating systems, distributions, installations and build environments don't get in the way of coding fun. For this quick start, we'll just use docker to set up a minimal Ubuntu installation and take it from there. However, you don't need Docker - similar instructions can be used to get things working on pretty much any Linux installation or a Mac Homebrew system, just don't come complaining if it doesn't work for you on some niche distribution (Arch, I'm looking at you!) or *nix with 6 months of cruft caking it up. Rather, get the docker image working and then figure out what the differences are.

*NOTE*: Ensure you have docker to begin with.

```
docker run -it ubuntu bash
```

This will give you a temporary docker environment.

### Grab Rust

NOTE: If you already have Rust in your environment, you don't need to bother with this. 


This will download and install Rust on Linux and OS X:

```
curl https://sh.rustup.rs -sSf | sh
```

If you are using Windows make sure you have Visual Studio 2015 with C++ support installed, download and run [rustup](https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-msvc/rustup-init.exe) and use the following command to setup msvc toolchain:

```
rustup default stable-x86_64-pc-windows-msvc
```

**NOTE**: We do not support the GNU toolset on Windows. Do not install it, and do not install any Rust version that uses it. If you see `x86_64-pc-windows-gnu` anywhere, you've done it wrong!

### Install and Build Parity

Next, grab the Parity repository:

```
git clone https://github.com/ethcore/parity && cd parity
```

For tests, also update submodules:
```
git submodule init
git submodule update
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

To install Parity, just build it and copy it to `/usr/local/bin`:

```
cargo build --release && cp target/release/parity /usr/local/bin
```

You can start a client and sync with the network with:

```
cargo run --release
```

To get help on the command line options for the `parity` client, use `--help`:

```
cargo run --release -- --help
```

## Quick Start with the One-line Binary Installer:

This method is way faster than building, though you won't get the cutting edge features and it only works on Ubuntu and Mac with Homebrew installed.

To use the script just run:

```
bash <(curl https://raw.githubusercontent.com/ethcore/parity/master/install-deps.sh -L)
```

This will install and configure the Parity client for you. 

On Ubuntu this script will also offer to install the [Netstats client](https://github.com/cubedro/eth-net-intelligence-api) and connect it to [stats.ethdev.com](https://stats.ethdev.com). The script will prompt you for entering the secret key needed for connecting to the Netstats server. You can learn the key by joining the Netstats [Skype group](http://is.gd/iwSaR9).

## Quick Start with Ubuntu Snappy on Raspberry Pi

There are Ubuntu Snappy builds for the RasPi, found in [Parity Snappy repository](github.com/ethcore/parity-snappy). TODO: installation instructions, @NikVolf / @GeneralBeck?

# Using Parity

Assuming you installed Parity, you can start the client with a simple `parity`. By default it will connect to other nodes on the Ethereum Homestead network and synchronise the blockchain. You can instead connect to the Morden testnet with `parity --testnet`. By default it will run in archive mode saving all intermediate information from previous blocks; to avoid doing this (and freeing up a couple of gigs of disk space) use a pruning option e.g. `parity --pruning=fast`.

If you want to interact with Parity at all, you'll want to enable the JSONRPC interface; to do that use `parity -j`. As well, there is also the Web interface; you can enable both with `parity -jw`. Since recent builds (1.2 and higher), running parity with `-jw` options are the default, thus no need to explicit. 

You can override the normal boot nodes and connect to your own nodes by using `parity --bootnodes ...`, e.g. you might run a local `geth` node and sync from that by running `parity --bootnodes enode://YOU_GETH_NODE_ID_HERE@127.0.0.1:30303`. You need to check geth's output to figure out what node ID is.

Use `parity --help` to get help on other options.

## JSON-RPC API

Assuming you start with JSONRPC API enabled (`parity -j`), then you can do most of the examples on [Ethereum's JSON-RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC). e.g.:

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

### Chrome Browser Console

In Parity version 1.1 and above, you can also use the Google Chrome console for web3 development. You'll need to run Parity with the `-jw` flags to enable the web integration interface. With this done, head to [http://localhost:8080/](http://localhost:8080/) and you'll be able to use Chrome's builtin console right away.
