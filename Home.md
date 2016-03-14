# Parity

## Quick Start

### Docker Environment

Docker's great for making sure differences between operating systems, distributions, installations and build environments don't get in the way of coding fun. For this quick start, we'll just use docker to set up a minimal Ubuntu installation and take it from there. However, you don't strictly need docker - similar instructions can almost certainly be used to get things working on pretty much any Linux installation or a Mac Homebrew system, but don't come complaining if it doesn't work for you on some niche distribution (Arch, I'm looking at you!) with 6 months of cruft caking it up. Rather, get the docker image working and then figure out what the differences are.

*NOTE*: Ensure you have docker to begin with.

```
docker run -it ubuntu bash
```

This will give you a temporary docker environment.

### Install and Build Parity

Now grab curl.

```
apt-get install curl -y
```

Then run the dependency install script:

```
bash <(curl https://raw.githubusercontent.com/ethcore/parity/master/install-deps.sh -L)
```

This will download and install all the build dependencies, in our case, that's Git, Make and GCC/G++, librocksdb, and Rust nightly. (It'll also prompt you whether you want to get and built parity & netstats. You can answer no to that as we'll do it here anyway.)

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
cargo test --release -p ethcore-util && cargo test -p ethcore
```

You can run the consensus tests with:

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

**NOTE: Prior to making our first tagged repository release, this probably won't work. This message will be removed after that happens.**

If you are using OS X or Ubuntu you can try the one-line installer script:

```
bash <(curl https://raw.githubusercontent.com/ethcore/parity/master/get-deps.sh -L)
```

This will install and configure the Parity client for you. 

On Ubuntu this script will also offer to install the [Netstats client](https://github.com/cubedro/eth-net-intelligence-api) and connect it to [stats.ethdev.com](https://stats.ethdev.com). The script will prompt you for entering the secret key needed for connecting to the Netstats server. You can learn the key by joining the Netstats [Skype group](http://is.gd/iwSaR9).

### Start the parity Client 
Start the client with `parity` other options are;

```
Usage:
  parity [options]
  parity [options] <enode>...

Options:
  -l --logging LOGGING     Specify the logging level.
  -j --jsonrpc             Enable the JSON-RPC API sever.
  --jsonrpc-url URL        Specify URL for JSON-RPC API server [default: 127.0.0.1:8545].

  --cache-pref-size BYTES  Specify the prefered size of the blockchain cache in bytes [default: 16384].
  --cache-max-size BYTES   Specify the maximum size of the blockchain cache in bytes [default: 262144].

  -h --help                Show this screen.
```

### JSON-RPC API

You can do most of the examples on [Ethereum's JSON-RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC):

````
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xYOUR_ADDRESS_HERE", "latest"],"id":1}'
````

(make sure you begin with parity -j)