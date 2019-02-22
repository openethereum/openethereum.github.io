---
title: Setup Parity Ethereum
---

- [Binaries](#binaries)
    - [One-line Binary Installer](#one-line-binary-installer)
    - [Use the Binaries](#use-the-binaries-for-your-platform)
- [Dependencies](#dependencies)
- [Building from Source](#building-from-source)
- [Building using Docker](#building-using-docker)
    - [Grab Rust](#grab-rust)
    - [Install and Build Parity Ethereum](#install-and-build-parity)
    - [A note on backing up your datadir with Docker](#a-note-on-backing-up-your-datadir-with-docker)
- [Ubuntu Snappy on Raspberry Pi](#ubuntu-snappy-on-raspberry-pi)


## Binaries

There are a few ways of proceeding here. You can build Parity Ethereum from the sources; you can launch Parity Ethereum using the binaries built [for the latest releases](https://github.com/paritytech/parity-ethereum/releases) for Linux, Mac/Homebrew and Windows or, if you're on an Ubuntu Snappy platform, just use our Snappy App. Other Unix-like environments should work (assuming you have the latex *nix installed); we're not going to expend much effort supporting them, though build PRs are welcome.

### One-line Binary Installer

This method is way faster than building from the sources, though it only works on Ubuntu and Mac with Homebrew installed. It will install and configure the Parity Ethereum client for you. To use the script just run:

```bash
$ bash <(curl https://get.parity.io -L)
```

### Use the binaries for your platform
Binaries for Parity Ethereum are provided. If you'd like to try these out, downloads are available on the [Releases page](https://github.com/paritytech/parity-ethereum/releases).
- Linux 
    - Download the latest release from the link above
    - Make the `parity` file executable by running `chmod u+x parity`
    - Launch Parity Ethereum with your favourite flags: `./parity --chain dev --jsonrpc-apis personal`
    
- Mac
  - Download the mac binary.
  - Open a terminal and navigate to the directory using `cd /path/to/binary/folder/`.
  - Make the binary executable by running `chmod +x parity`.
  - You can now double click on the binary.
  
- Windows
Download the binary and double click on it.

## Dependencies

For Linux systems:

- Ubuntu, Debian

    ```bash
    $ apt-get install build-essential cmake libudev-dev
    ```

- CentOS


    ```bash
    $ yum install libudev-devel
    $ yum group install "Development Tools"
    ```

## Building from source

To build Parity Ethereum from source follow the instructions in the [README](https://github.com/paritytech/parity-ethereum/blob/master/README.md).

For Ethereum key management [Ethstore](https://github.com/paritytech/parity-ethereum/tree/master/accounts/ethstore) can be used. To compile it use

```bash
$ cargo build --release -p ethstore-cli
```

in the root Parity Ethereum directory and for usage instructions:

```bash
$ ./target/release/ethstore --help
```

## Building using Docker

Docker is great for making sure differences between operating systems, distributions, installations and build environments don't get in the way of coding fun. For this quick start, we'll use docker to set up a minimal Ubuntu installation and take it from there. However, you don't need Docker - similar instructions can be used to get things working on pretty much any Linux installation or a Mac Homebrew system, just don't come complaining if it doesn't work for you on some niche distribution (Arch, I'm looking at you!) or *nix with 6 months of cruft caking it up. Instead, get the docker image working and then figure out what the differences are.

*NOTE*: Ensure you have docker to begin with.

```bash
$ docker run -it ubuntu bash
```

This will give you a temporary docker environment.


### Grab Rust

*NOTE*: If you already have Rust in your environment, you don't need to bother with this.

This will download and install Rust on Linux and OS X:

```bash
$ curl https://sh.rustup.rs -sSf | sh
```

If you are using Windows make sure Visual Studio 2015 with C++ support is installed. Ensure you run all of the next stuff in the `VS2015 x64 Native Tools Command Prompt`.

*NOTE*: We do not support the GNU toolset on Windows. Do not install it, and do not install any Rust version that uses it. If you see `x86_64-pc-windows-gnu` anywhere, you've done it wrong!

Download and run [rustup](https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-msvc/rustup-init.exe) and use the following command to setup the MSVC toolchain:

```bash
$ rustup default stable-x86_64-pc-windows-msvc
```

### Install and Build Parity

Next, grab the Parity Ethereum repository:

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

To install Parity Ethereum on Linux and Mac OS, just build it and copy it to `/usr/local/bin`:

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

### A note on backing up your datadir with Docker

In case you need to persist the blockchain files, keys etc., you should run the image with the `--base-path` option and then mount it, e.g.:

```bash
$ docker run --name parity -v /srv/parity:/mnt ethcore/parity:beta --base-path /mnt
```


## Ubuntu Snappy on Raspberry Pi

There are Ubuntu Snappy builds for the RasPi, found in [Parity Ethereum Snappy repository](https://github.com/paritytech/parity-snappy).
