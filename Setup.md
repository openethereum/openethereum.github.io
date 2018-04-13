---
title: Setup Parity
---

- [Binaries](#binaries)
    - [One-line Binary Installer](#one-line-binary-installer)
    - [Experimental Installers](#experimental-installers)
- [Dependencies](#dependencies)
- [Building from Source](#building-from-source)
- [Building using Docker](#building-using-docker)
    - [Grab Rust](#grab-rust)
    - [Install and Build Parity](#install-and-build-parity)
    - [A note on backing up your datadir with Docker](#a-note-on-backing-up-your-datadir-with-docker)
- [Ubuntu Snappy on Raspberry Pi](#ubuntu-snappy-on-raspberry-pi)
- [Ethereum Mainnet Hardware Requirements](#ethereum-mainnet-hardware-requirements)
    - [Full Node](#full-node)
    - [Light Node](#light-node)
    - [Storage Requirements Overview](#storage-requirements-overview)


### Binaries

There are a few ways of proceeding here. You can build Parity from the sources; you can install Parity from our [binary releases](https://github.com/paritytech/parity/releases) for Ubuntu, Mac/Homebrew and Windows or, if you're on an Ubuntu Snappy platform, just use our Snappy App. Other Unix-like environments should work (assuming you have the latex *nix installed); we're not going to expend much effort supporting them, though build PRs are welcome.

##### One-line Binary Installer

This method is way faster than building, though you won't get the cutting edge features and it only works on Ubuntu and Mac with Homebrew installed. To use the script just run:

```bash
$ bash <(curl https://get.parity.io -kL)
```

This will install and configure the Parity client for you. On Ubuntu this script will also offer to install the [Netstats client](https://github.com/cubedro/eth-net-intelligence-api) and connect it to [ethstats.net](https://ethstats.net). The script will prompt you for entering the secret key needed for connecting to the Netstats server. You can learn the key by joining the Netstats [Skype group](http://is.gd/iwSaR9).

##### Experimental Installers
There are also experimental installers for OS X and Windows which will install Parity as a system service. Parity will always run in the background and hibernate when not being used with the bundled wallet. If you'd like to try these out, downloads are available on the [Releases page](https://github.com/paritytech/parity/releases).

### Dependencies

For linux systems:

- Ubuntu, Debian

    ```bash
    $ apt-get install build-essential openssl libssl-dev libudev-dev
    ```

- CentOS


    ```bash
    $ yum install openssl libssl-devel libudev-devel openssl-devel
    $ yum group install "Development Tools"
    ```

### Building from source

To simply build Parity from source follow the instructions in the [README](https://github.com/paritytech/parity/blob/master/README.md).

For Ethereum key management [Ethstore](https://github.com/paritytech/ethstore) can be used. To compile it use

```bash
$ cargo build --release -p ethstore --features=ethstore-cli
```

in the root Parity directory and for usage instructions:

```bash
$ ./target/release/deps/ethstore --help
```

### Building using Docker

Docker is great for making sure differences between operating systems, distributions, installations and build environments don't get in the way of coding fun. For this quick start, we'll just use docker to set up a minimal Ubuntu installation and take it from there. However, you don't need Docker - similar instructions can be used to get things working on pretty much any Linux installation or a Mac Homebrew system, just don't come complaining if it doesn't work for you on some niche distribution (Arch, I'm looking at you!) or *nix with 6 months of cruft caking it up. Rather, get the docker image working and then figure out what the differences are.

*NOTE*: Ensure you have docker to begin with.

```bash
$ docker run -it ubuntu bash
```

This will give you a temporary docker environment.


##### Grab Rust

*NOTE*: If you already have Rust in your environment, you don't need to bother with this.

This will download and install Rust on Linux and OS X:

```bash
$ curl https://sh.rustup.rs -sSf | sh
```

If you are using Windows make sure you have Visual Studio 2015 with C++ support installed. Ensure you run all of the next stuff in the `VS2015 x64 Native Tools Command Prompt`.

*NOTE*: We do not support the GNU toolset on Windows. Do not install it, and do not install any Rust version that uses it. If you see `x86_64-pc-windows-gnu` anywhere, you've done it wrong!

Download and run [rustup](https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-msvc/rustup-init.exe) and use the following command to setup the MSVC toolchain:

```bash
$ rustup default stable-x86_64-pc-windows-msvc
```

##### Install and Build Parity

Next, grab the Parity repository:

```bash
$ git clone https://github.com/paritytech/parity
$ cd parity
```

For tests, also update submodules:

```bash
$ git submodule init
$ git submodule update
```

You can build with:

```bash
$ cargo build
```

You can run the unit tests with:

```bash
$ ./test.sh
```

You can run just the consensus tests with:

```bash
$ cargo test --release --features ethcore/json-tests -p ethcore
```

To install Parity on Linux and Mac OS, just build it and copy it to `/usr/local/bin`:

```bash
$ cargo build --release && cp target/release/parity /usr/local/bin
```

For Windows, use `copy` it into `C:/Windows`:

```bash
$ cargo build --release
$ copy target/release/parity C:/Windows
```

You can start a client and sync with the network with:

```bash
$ cargo run --release
```

To get help on the command line options for the `parity` client, use `--help`:

```bash
$ cargo run --release --help
```

##### A note on backing up your datadir with Docker

In case you need to persist the blockchain files, keys etc., you should run the image with the `--base-path` option and then mount it, e.g.:

```bash
$ docker run --name parity -v /srv/parity:/mnt ethcore/parity:beta --base-path /mnt
```


### Ubuntu Snappy on Raspberry Pi

There are Ubuntu Snappy builds for the RasPi, found in [Parity Snappy repository](https://github.com/paritytech/parity-snappy).

### Ethereum Mainnet Hardware Requirements

##### Full Node

Running a full node with the stadard configuration for the Ethereum Mainnet requires a lot of computer ressources. The blockchain download and validation process is particularly heavy on CPU and disk IO. It is therefore recommended to run a full node on a computer with multi-core CPU, 4GB RAM and a SSD drive with at least 60GB free space. Internet connection can also be a limiting factor. A decent DSL connection is required.

Computer using HDD hard drive are advised to run a [Light Node](#light-node).

##### Light Node

Running a light node using the flag `--light` does not require to download and perform validation of the whole blockchain. A light node relies on full node peers to receive block headers and verify transactions. It is therefore far less ressource demanding than a full node.

A computer or mobile phone with single core CPU, 512MB RAM and a HDD with 128MB free space is recommended to run a light node.

#### Storage Requirements Overview

Indicative data storage requirement from September 2017 syncing Ethereum Mainnet (ETH) and Classic (ETC) chains :
```
| ID | Pruning / DB Config | Verification    | Available History          | ETH        | ETC        | EXP        | MSC        | Parity CLI Options                         |
|====|=====================|=================|============================|============|============|============|============|============================================|
| 00 | archive +Fat +Trace | Full/No-Warp    | All Blocks + States        | 385     GB |  90.0   GB |   5.6   GB |  25.0   GB | --pruning archive --tracing on --fat-db on |
| 01 | archive +Trace      | Full/No-Warp    | All Blocks + States        | 334     GB |  90.0   GB |   5.8   GB |  21.0   GB | --pruning archive --tracing on             |
| 02 | archive             | Full/No-Warp    | All Blocks + States        | 326     GB |  91.0   GB |   5.5   GB |  30.0   GB | --pruning archive                          |
| 03 | fast +Fat +Trace    | Full/No-Warp    | All Blocks + Recent States |  37.0   GB |  13.0   GB |   1.3   GB |   3.5   GB | --tracing on --fat-db on                   |
| 04 | fast +Trace         | Full/No-Warp    | All Blocks + Recent States |  34.0   GB |  13.0   GB |   1.2   GB |   3.5   GB | --tracing on                               |
| 05 | fast                | Full/No-Warp    | All Blocks + Recent States |  26.0   GB |   9.7   GB |   1.1   GB |   3.0   GB | --no-warp                                  |
| 06 | fast +Warp          | PoW-Only/Warp   | All Blocks + Recent States |  25.0   GB |   9.6   GB |   0.96  GB |   2.6   GB |                                            |
| 07 | fast +Warp -Ancient | No-Ancient/Warp | Recent Blocks + States     |   5.3   GB |   2.9   GB |   0.13  GB |   0.19  GB | --no-ancient-blocks                        |
| 08 | light               | Headers/Light   | No Blocks + No State       |       5 MB |       3 MB |       5 MB |       4 MB | --light                                    |
```
