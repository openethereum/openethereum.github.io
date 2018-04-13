---
title: FAQ - Building, Installation, and Testing
---

## How do I install Parity?

There are a few ways of proceeding here. You can build Parity from the sources; you can install Parity from our [binary releases](https://github.com/paritytech/parity/releases) for Ubuntu, Mac/Homebrew and Windows or, if you're on an Ubuntu Snappy platform, just use our Snappy App. Other Unix-like environments should work (assuming you have the latex *nix installed); we're not going to expend much effort supporting them, though build PRs are welcome.

On MacOS and Ubuntu you can use the _One-Line Binary Installer_, to use the script just run:


```bash
bash <(curl https://get.parity.io -Lk)
```

This method is way faster than building, though you won't get the cutting edge features and it only works on Ubuntu and Mac with Homebrew installed.

## Are there any installers available?

Yes, there are also experimental installers for MacOS X and Windows which will install Parity as a system service. Parity will always run in the background and hibernate when not being used with the bundled wallet.

You can download the latest versions from the [releases page on GitHub](https://github.com/paritytech/parity/releases).

## Where can I find official releases?

You can download the latest versions from the [releases page on GitHub](https://github.com/paritytech/parity/releases). :)

Make sure you check the `md5sum`, on your command-line, type

```bash
md5sum parity_1.5.7_amd64.deb
```

and compare the output with the provided checksum at the [releases page on GitHub](https://github.com/paritytech/parity/releases).

```bash
d8fbd5f71b5b436e1a45d7359e26bc0c  parity_1.5.7_amd64.deb
```

## Isn't there any browser plug-in available?

Yes, there is the [Parity Chrome Extension](https://github.com/paritytech/parity-extension) available.

## Are builds for ARM devices available (i.e., Raspberry Pi)?

There are builds for ARMhf (ARMv7) and ARM64 on our releases page on GitHub.

In addition, The [EthRaspbian](https://github.com/diglos/pi-gen) project provides Debian-flavored images for the Raspberry Pi which includes Parity as default client. In addition, the [EthArmbian](https://github.com/diglos/userpatches) project supports Odroid C2 devices.

## What is the difference between stable and beta releases?

Stable releases are the most tested and will not get any new features. It is the version that we recommend to most users in a production environment.

Beta releases will be augmented with new features between the different releases which often require more dedicated long-term testing and could have a couple of issues. If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/paritytech/Parity/issues/new).

## How do I build the cutting-edge version of Parity?

To build Parity from source simply follow the instructions in the [README](https://github.com/paritytech/parity/blob/master/README.md#build-dependencies). Once you have cloned the source directory and installed Rust, just use cargo and copy the resulting binary:

```bash
cargo build --release
```

For Ethereum key management [Ethstore](https://github.com/paritytech/ethstore) can be used. To compile it use:

```bash
cargo build --release -p ethstore --features=ethstore-cli
```

in the root Parity directory and for usage instructions: `./target/release/deps/ethstore --help`.

## How do I install Parity after building?

- On Windows: `copy target/release/parity.exe C:/Windows`
- On Mac/Linux: `cp -f target/release/parity /usr/local/bin`

## How do I update Parity?

Latest versions of Parity contain an auto-update feature, which makes sure your client is always updated to the latest version. You can disable the auto-update feature by running Parity with the `--no-download` flag.

If you use older versions of Parity or want to manually upgrade, simply follow the instructions above to download (or build) and install. The new version will replace the old. Nothing additional is needed.

## I'm getting build errors, what can I do?

Make sure you are using the latest stable rust compiler. Update with `rustup update` when using rustup or `multirust update` when using multirust.

## I'm trying to build but get 'libudev' or 'libssl' errors!?!

Install required packages `libudev-dev` and `libssl-dev`.

## The one-line installer fails with "SSL peer handshake failed" on macOS!?

If you encounter error `curl: (35) SSL peer handshake failed, the server most likely requires a client certificate to connect` then run the following commands prior to the one-liner mentioned above. These commands will install and update your OpenSSL and Curl sym-links and reload your shell session for secure communication:

```bash
# update Homebrew
$ brew update

# upgrade OpenSSL and update symlinks
$ brew upgrade openssl
$ brew link openssl --force

# install cURL and update symlinks
$ brew install --with-openssl curl
$ brew link curl --force

# reload shell session
$ source ~/.bash_profile

# show location of binaries and current version
$ which curl && which openssl && curl --version
```

## I'm trying to build on Windows but I get 'link failed' errors mentioning 'kernel32.lib'!?!

Make sure you have set up your 64-bit variables in the build shell before issuing `cargo build`:

````
"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64\vcvars64.bat"
````

## How can I cross-build Parity for other platforms (e.g., ARM)?

See the documentation for [docker build for ARM](https://github.com/paritytech/parity-snappy/wiki/Docker-build-for-ARM-ARM64).

## How can I build and run tests?

First make sure you have the cross-client consensus tests installed:

```
git submodule init
git submodule update
```

Then you just run `./test.sh`. You can run individual tests with `cargo test -p <package-name>`.
