### What is Parity?

Parity is an Ethereum client, written from the ground-up for correctness-verifiability, modularization, low-footprint and high-performance. To this end it utilizes the Rust language, a hybrid imperative/object-orientated/functional language with an emphasis on efficiency. It is professionally developed by _Parity Technologies_ (formerly _Ethcore_); we aim to have all important logic 100% unit-tested, all public APIs 100% documented, all code reviewed by multiple peers, and follow a pipelined #6-ish-week release cycle similar to the Rust compiler. [Read more on our tenets](https://github.com/ethcore/parity/wiki#our-tenets).

---

### Building, Installation, and Testing

#### How do I install Parity?

There are a few ways of proceeding here. You can build Parity from the sources; you can install Parity from our [binary releases](https://github.com/ethcore/parity/releases) for Ubuntu, Mac/Homebrew and Windows or, if you're on an Ubuntu Snappy platform, just use our Snappy App. Other Unix-like environments should work (assuming you have the latex *nix installed); we're not going to expend much effort supporting them, though build PRs are welcome.

On MacOS and Ubuntu you can use the _One-Line Binary Installer_, to use the script just run:


```bash
bash <(curl https://get.parity.io -Lk)
```

This method is way faster than building, though you won't get the cutting edge features and it only works on Ubuntu and Mac with Homebrew installed.

#### Are there any installers available?

Yes, there are also experimental installers for MacOS X and Windows which will install Parity as a system service. Parity will always run in the background and hibernate when not being used with the bundled wallet.

You can download the latest versions from the [releases page on GitHub](https://github.com/ethcore/parity/releases).

#### Where can I find official releases?

You can download the latest versions from the [releases page on GitHub](https://github.com/ethcore/parity/releases). :)

Make sure you check the `md5sum`, on your command-line, type

```bash
md5sum parity_1.5.7_amd64.deb
```

and compare the output with the provided checksum at the [releases page on GitHub](https://github.com/ethcore/parity/releases).

```bash
d8fbd5f71b5b436e1a45d7359e26bc0c  parity_1.5.7_amd64.deb
```

#### Isn't there any browser plug-in available?

Yes, there is the [Parity Chrome Extension](https://github.com/ethcore/parity-extension) available.

#### Are builds for ARM devices available (i.e., Raspberry Pi)?

There are builds for ARMhf (ARMv7) and ARM64 on our releases page on GitHub.

In addition, The [EthRaspbian](https://github.com/diglos/pi-gen) project provides Debian-flavored images for the Raspberry Pi which includes Parity as default client. In addition, the [EthArmbian](https://github.com/diglos/userpatches) project supports Odroid C2 devices.

#### What are the system requirements for running Parity?

Parity is targeted to run on any device. However, it depends entirely on which chain you want to synchronize your device with. In principle, you can run Parity on a RaspberryPi Zero and still get acceptable performance.

#### What is the difference between stable and beta releases?

Stable releases are the most tested and will not get any new features. It is the version that we recommend to most users in a production environment.

Beta releases will be augmented with new features between the different releases which often require more dedicated long-term testing and could have a couple of issues. If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/ethcore/Parity/issues/new).

#### How do I build the cutting-edge version of Parity?

To build Parity from source simply follow the instructions in the [README](https://github.com/ethcore/parity/blob/master/README.md#build-dependencies). Once you have cloned the source directory and installed Rust, just use cargo and copy the resulting binary:

```bash
cargo build --release
```

For Ethereum key management [Ethstore](https://github.com/ethcore/ethstore) can be used. To compile it use:

```bash
cargo build --release -p ethstore --features=ethstore-cli
```

in the root Parity directory and for usage instructions: `./target/release/deps/ethstore --help`.

#### How do I install Parity after building?

- On Windows: `copy target/release/parity.exe C:/Windows`
- On Mac/Linux: `cp -f target/release/parity /usr/local/bin`

#### How do I update Parity?

Latest versions of Parity contain an auto-update feature, which makes sure your client is always updated to the latest version. You can disable the auto-update feature by running Parity with the `--no-download` flag.

If you use older versions of Parity or want to manually upgrade, simply follow the instructions above to download (or build) and install. The new version will replace the old. Nothing additional is needed.

#### I'm getting build errors, what can I do?

Make sure you are using the latest stable rust compiler. Update with `rustup update` when using rustup or `multirust update` when using multirust.

#### I'm trying to build but get 'libudev' or 'libssl' errors!?!

Install required packages `libudev-dev` and `libssl-dev`.

#### I'm trying to build on Windows but I get 'link failed' errors mentioning 'kernel32.lib'!?!

Make sure you have set up your 64-bit variables in the build shell before issuing `cargo build`:

````
"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64\vcvars64.bat"
````

#### How can I cross-build Parity for other platforms (e.g., ARM)?

See the documentation for [docker build for ARM](https://github.com/ethcore/parity-snappy/wiki/Docker-build-for-ARM-ARM64).

#### How can I build and run tests?

First make sure you have the cross-client consensus tests installed:

```
git submodule init
git submodule update
```

Then you just run `./test.sh`. You can run individual tests with `cargo test -p <package-name>`.

---

### Basic Operations, Configuration, and Synchronization

#### How do I run Parity?

After building Parity from source:

- On Windows run: `target/release/parity.exe`
- On MacOS and Linux run: `target/release/parity`

After installing Parity, just run `parity` from command line. See also [how do I configure Parity](https://github.com/ethcore/parity/wiki/FAQ#how-do-i-configure-parity) below.

#### How do I configure Parity?

You can configure your Parity client by passing command-line flags to the executable. For usage instructions, see `parity --usage` and for a list of available flags, see `parity --help` or check out the [CLI Options here](https://github.com/ethcore/parity/wiki/Configuring-Parity#cli-options).

In additoin, Parity can be configured using a [TOML](https://github.com/toml-lang/toml) file. The file can be generated using the [Parity Config Generator](https://ethcore.github.io/parity-config-generator/). To start parity with a config file, the file needs to be located in:

* Windows: `%UserProfile%\AppData\Roaming\Parity\Ethereum\config.toml`
* Linux: `~/.local/share/io.parity.ethereum/config.toml`
* macOS: `$HOME/Library/Application Support/io.parity.ethereum/config.toml`

To use a custom path run `$ parity --config path/to/config.toml`. Read more on [Parity config file here](https://github.com/ethcore/parity/wiki/Configuring-Parity#config-file).

#### Parity doesn't start on Windows, and fails with 'api-ms-win-crt-math-l1-1-0.dll' missing!?!

![parity_error](https://cloud.githubusercontent.com/assets/2982011/18855854/1d3b2c22-8424-11e6-8329-1dbe5edf6a5a.png)

Install the following windows update: https://support.microsoft.com/en-us/kb/2999226

#### What are the different Parity synchronization and pruning modes?

Since Parity 1.2 state-trie pruning is enabled by default (`--pruning fast`). You can disable it by setting the pruning method to `archive` which keeps all state trie data:

    --pruning METHOD               Configure pruning of the state/storage trie. METHOD
                                   may be one of auto, archive, fast:
                                   archive - keep all state trie data. No pruning.
                                   fast - maintain journal overlay. Fast but 50MB used.
                                   auto - use the method most recently synced or
                                   default to fast if none synced (default: auto).

To reduce the size of the kept pruning history, you can set the minimum number of recent states with the `--pruning-history` flag:

    --pruning-history NUM          Set a minimum number of recent states to keep when pruning
                                   is active. (default: 1200).

By default, 1200 states are kept.

#### Why is warp synchronization so fast?

Warp sync extends previous versions of the protocol with full state snapshots. These snapshots can be used to quickly get a full copy of the state at a given block. Every 30,000 blocks, nodes will take a consensus-critical snapshot of that block's state. Any node can fetch these snapshots over the network, enabling a fast sync. These snapshots have been designed with out-of-order restoration in mind -- it isn't required to get any given chunk before another.

See also [what is Parity's “warp” sync](http://ethereum.stackexchange.com/q/9991) at Ethereum Stack Exchange and read [the full specification at the Wiki here](https://github.com/ethcore/parity/wiki/Warp-Sync-Snapshot-Format#manifest).

#### How can I tell if Parity is fully synchronized?

To check whether Parity is currently synchronizing the chain, you can open the Web3 console and type:

```JavaScript
web3.eth.syncing
```

Returning `true` means the client is still busy fetching the chain. To get the latest block number, you can type:

```JavaScript
web3.eth.getBlock('latest').number
```

The Parity/Web3 console can be accessed via the UI in the 'Applications' tab.

#### What can I do when Parity has trouble getting in sync?

Parity is running but seems to remain at the same block for a long time.

1. Make sure you have up to date version of Parity.
2. Go to http://time.is/ and ensure it says "Your time is exact":

   ![image](https://cloud.githubusercontent.com/assets/138296/19265409/5e4a89ce-8fa5-11e6-8ec6-6c72c138ee48.png)

   If it isn't get it synced. Your machine will not automatically do this; my machine is routinely 1-2 seconds out of sync which is enough to disrupt peer discovery.

3. Make sure your network does not block UDP traffic.
4. Remove your nodes file (`$HOME/.parity/network/nodes.json`) while Parity is not running.
5. Wait a bit longer.

If the problem persists, please run Parity with `-l sync=trace` option for a while and post the output [here](https://github.com/ethcore/parity/issues/2447).

#### Sync is stuck around block 2,421,000 or 2,675,000!?!

These blocks are quite heavy on the IO and take a long time to import, especially on machines with HDD. Consider using `--warp` or switching to SSD drive.

#### Is there any Parity light client available?

The light client is not finished yet, but on the [road map for upcoming releases](https://github.com/ethcore/parity/projects/2).

#### How do I enable transaction tracing (indexing)?

Transaction tracing, available in Parity 1.1 and above, enables all `CALL`/`CREATE` information (i.e. "internal transactions") to be efficiently searched and filtered. More information can be found on the dedicated [Wiki page](JSONRPC-trace-module).

#### How do I create a new account?

Either by enabling the `personal` web3 API and typing

```JavaScript
web3.personal.newAccount()
```

in the Web3 console, or by using the Parity Wallet UI to create or import accounts:

![Parity Wallet Create New Account](https://i.imgur.com/Lnpczaa.png)

#### What is the trusted signer?

#### How can I run Parity in Docker?

Docker images for Parity [are available via](https://hub.docker.com/r/ethcore/parity/):

```bash
docker pull ethcore/parity:beta-release
```

You can view the [Dockerfile here](https://github.com/ethcore/parity/blob/master/docker/ubuntu/Dockerfile).

#### Can I run Parity as a daemon or system service?

The versions installed with the Windows and MacOS X installers automatically configure and enable a system service.

To manually enable the system service for Parity installed with Homebrew, run:

```bash
sudo launchctl enable io.parity.ethereum
sudo launchctl start io.parity.ethereum
```

To manually run Parity in daemon mode in background, run `parity daemon`.

#### How can I stop a Parity node cleanly?

If you run Parity in a terminal, hitting `CTRL+C` will cleanly exit it. Make sure to wait a couple of seconds.

To stop Parity started as system service, try to stop it with `systemctl` (Linux) or `launchctl` (MacOS):

```bash
sudo launchctl stop io.parity.ethereum
```

Another way, is to issue a _hang-up_ (HUP) call for the Parity client, i.e.:

```bash
killall -HUP parity
```

---

### Backup, Restore, and Files

#### Where can I find Parity's local files?

On Windows blockchain data is located in `C:\Users\You\AppData\Local\Parity\Ethereum`
while keys and configuration are kept in `C:\Users\You\AppData\Roaming\Parity\Ethereum`

On MacOS: `/Users/you/Library/Application Support/io.parity.ethereum`

On Linux: `/home/you/.local/share/io.parity.ethereum`

#### How can I backup my accounts and keys? How can I restore my keys?

See [[Backing-up-&-Restoring]].

#### Can I import my keys from Geth?

They get imported automatically. :)

#### Can I import accounts not created with Parity or Geth (e.g., MyEtherWallet)?

_Parity 1.5 and above:_

- Simply use Parity Wallet's "New Account" function on the "Accounts" page.

_Parity 1.4 and below:_

- Simply copy it into your `$HOME/.parity/keys` directory. Parity will import all keys found there.

#### What are the Parity disk space needs?

This entirely depends on the selected chain and whether state trie pruning is enabled or not.

For example `--pruning archive`, which disables state trie pruning, the chain data requires around 25 GB of space for the public foundation chain. The default `--pruning fast` reduces the blockchain size to around 7 GB.

#### How do I backup my blockchain?

You can just export it to a file:

```bash
parity export $HOME/ethereum-chain-backup.rlp
```

#### How do I restore my blockchain from a previous backup?

Just use `import`:
```bash
parity import $HOME/ethereum-chain-backup.rlp
```

#### Can I import the chain from Geth?

Yes, see [[Importing a Chain from Geth]].

#### How to delete the blockchain to initiate full re-sync without deleting wallet info?

_For Parity 1.5 and above:_

- Execute `parity db kill`.
- Relevant options are `--chain=*` `--pruning=*` and `--db-path/--base-path`, i.e. to delete `morden` run:

```bash
parity db kill --chain=morden
```

_For Parity 1.4 and below:_

- Execute `rm -rf ~/.parity/906a34e69aec8c0d`
- This will remove the local chain database.

Always back up your keys (~/.parity/keys) before doing anything like that though.


---

### CLI, Mining, and Networks

#### How do I mine with Parity?

#### Which chains are supported by Parity?

#### What are the different consensus engines available for Parity?

#### How do I get Ether for the Morden, Ropsten, or Kovan testnets?

#### Does Parity provide a JavaScript console?

#### None of my JSONRPC requests work, they all fail with no output!?!

As a safeguard against a certain type of attack, Parity requires you to use a special content type. Just add `-H "Content-Type: application/json"` to your `curl` command.

#### When I start Parity with the 'homestead_test.json' I get a 0 blocks chain, and mining never seals a block!?!

`homestead_test.json` is a file meant for testing on a chain very similar to the Homestead chain. As such it is of equivalent (substantial) difficulty to mine on it. Rather what you need is something of minimal mining difficulty, like Morden. See more instructions [here](https://github.com/ethcore/parity/wiki/Private-chains).

#### Why do my transactions not get mined?

Your gas price is probably too low. Try increasing the gas price to 20Gwei by appending `, gasPrice: 20000000000` into your transaction spec or `sendTransaction` options.

#### All my 'eth_sendTransaction' calls only return '0x00000000...00000000' as return value!?!

You're probably trying to send from a locked account. Ensure you use the `--unlock` (to specify which account(s) to unlock) and `--password` (to pass in the file contain any passwords needed to unlock those acocunts) so that the account(s) from which you're sending the transaction are unlocked.

---

### User Interface, Wallet and DApps

#### How can I access the Parity Wallet?

#### Can I use Mist with Parity?

#### Can I send Bitcoin to my Parity wallet?

#### Can I create multisignature wallets with Parity?

#### How can I compile and deploy contracts with Parity?

#### How can I interact with existing contracts?

#### What DApps are available for Parity?

#### How can I write a DApp for Parity?

#### Does Parity support the Swarm and Whisper sub-protocols?

---

### Troubleshooting (Yelp!!!!11)

#### Double-clicking the Parity icon doesn't do anything!?!

Probably your node is already running (or another node is running, i.e., Geth) and another instance of Parity can not be launched with the same configuration (ports, dapps, web interface, etc.).

#### How can I make Parity to write logs?

You can control the logging level with `-l` and define a `--log_file` location:

```bash
  -l --logging LOGGING           Specify the logging level. Must conform to the same
                                 format as RUST_LOG. (default: None)
  --log-file FILENAME            Specify a filename into which logging should be
                                 appended. (default: None)
```

See also: [How to make Parity write logs](http://ethereum.stackexchange.com/questions/3331/how-to-make-parity-write-logs)?

#### Where are the logs when Parity runs in daemon mode?

Use

```bash
parity daemon --log-file /path/to/parity.log
```

See also: [Where are the parity log files in daemon mode](http://ethereum.stackexchange.com/questions/11363/where-are-the-parity-log-files-in-daemon-mode)?

#### How can I report an issue with Parity?

If you have any urgent matters, get in [touch with us on Gitter](https://gitter.im/ethcore/Parity). If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/ethcore/Parity/issues/new).

#### I just scrolled all the way down here and didn't find what I'm looking for!?!

Find more questions [tagged with `[Parity]` on Ethereum Stack Exchange]() or [ask a new question](https://ethereum.stackexchange.com/questions/ask).

However, if you have any urgent matters, get in [touch with us on Gitter](https://gitter.im/ethcore/Parity). If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/ethcore/Parity/issues/new).
