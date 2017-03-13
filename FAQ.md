### What is Parity?

Parity is an Ethereum client, written from the ground-up for correctness-verifiability, modularization, low-footprint and high-performance. To this end it utilizes the Rust language, a hybrid imperative/object-orientated/functional language with an emphasis on efficiency. It is professionally developed by _Parity Technologies_ (formerly _Ethcore_); we aim to have all important logic 100% unit-tested, all public APIs 100% documented, all code reviewed by multiple peers, and follow a pipelined #6-ish-week release cycle similar to the Rust compiler. [Read more on our tenets](https://github.com/ethcore/parity/wiki#our-tenets).

Table of Contents:

 * [Building, Installation, and Testing](#building-installation-and-testing)
    * [How do I install Parity?](#how-do-i-install-parity)
    * [Are there any installers available?](#are-there-any-installers-available)
    * [Where can I find official releases?](#where-can-i-find-official-releases)
    * [Isn't there any browser plug-in available?](#isnt-there-any-browser-plug-in-available)
    * [Are builds for ARM devices available (i.e., Raspberry Pi)?](#are-builds-for-arm-devices-available-ie-raspberry-pi)
    * [What are the system requirements for running Parity?](#what-are-the-system-requirements-for-running-parity)
    * [What is the difference between stable and beta releases?](#what-is-the-difference-between-stable-and-beta-releases)
    * [How do I build the cutting-edge version of Parity?](#how-do-i-build-the-cutting-edge-version-of-parity)
    * [How do I install Parity after building?](#how-do-i-install-parity-after-building)
    * [How do I update Parity?](#how-do-i-update-parity)
    * [I'm getting build errors, what can I do?](#im-getting-build-errors-what-can-i-do)
    * [I'm trying to build but get 'libudev' or 'libssl' errors!?!](#im-trying-to-build-but-get-libudev-or-libssl-errors)
    * [I'm trying to build on Windows but I get 'link failed' errors mentioning 'kernel32.lib'!?!](#im-trying-to-build-on-windows-but-i-get-link-failed-errors-mentioning-kernel32lib)
    * [How can I cross-build Parity for other platforms (e.g., ARM)?](#how-can-i-cross-build-parity-for-other-platforms-eg-arm)
    * [How can I build and run tests?](#how-can-i-build-and-run-tests)
 * [Basic Operations, Configuration, and Synchronization](#basic-operations-configuration-and-synchronization)
    * [How do I run Parity?](#how-do-i-run-parity)
    * [How do I configure Parity?](#how-do-i-configure-parity)
    * [Parity doesn't start on Windows, and fails with 'api-ms-win-crt-math-l1-1-0.dll' missing!?!](#parity-doesnt-start-on-windows-and-fails-with-api-ms-win-crt-math-l1-1-0dll-missing)
    * [What are the different Parity synchronization and pruning modes?](#what-are-the-different-parity-synchronization-and-pruning-modes)
    * [Why is warp synchronization so fast?](#why-is-warp-synchronization-so-fast)
    * [How can I tell if Parity is fully synchronized?](#how-can-i-tell-if-parity-is-fully-synchronized)
    * [What can I do when Parity has trouble getting in sync?](#what-can-i-do-when-parity-has-trouble-getting-in-sync)
    * [Sync is stuck around block 2,421,000 or 2,675,000!?!](#sync-is-stuck-around-block-2421000-or-2675000)
    * [Is there any Parity light client available?](#is-there-any-parity-light-client-available)
    * [How do I enable transaction tracing (indexing)?](#how-do-i-enable-transaction-tracing-indexing)
    * [How do I create a new account?](#how-do-i-create-a-new-account)
    * [What is the trusted signer?](#what-is-the-trusted-signer)
    * [How can I run Parity in Docker?](#how-can-i-run-parity-in-docker)
    * [Can I run Parity as a daemon or system service?](#can-i-run-parity-as-a-daemon-or-system-service)
    * [How can I stop a Parity node cleanly?](#how-can-i-stop-a-parity-node-cleanly)
 * [Backup, Restore, and Files](#backup-restore-and-files)
    * [Where can I find Parity's local files?](#where-can-i-find-paritys-local-files)
    * [How can I backup my accounts and keys? How can I restore my keys?](#how-can-i-backup-my-accounts-and-keys-how-can-i-restore-my-keys)
    * [Can I import my keys from Geth?](#can-i-import-my-keys-from-geth)
    * [Can I import accounts not created with Parity or Geth (e.g., MyEtherWallet)?](#can-i-import-accounts-not-created-with-parity-or-geth-eg-myetherwallet)
    * [What are the Parity disk space needs?](#what-are-the-parity-disk-space-needs)
    * [How do I backup my blockchain?](#how-do-i-backup-my-blockchain)
    * [How do I restore my blockchain from a previous backup?](#how-do-i-restore-my-blockchain-from-a-previous-backup)
    * [Can I import the chain from Geth?](#can-i-import-the-chain-from-geth)
    * [How to delete the blockchain to initiate full re-sync without deleting wallet info?](#how-to-delete-the-blockchain-to-initiate-full-re-sync-without-deleting-wallet-info)
 * [CLI, Mining, and Networks](#cli-mining-and-networks)
    * [How do I mine with Parity?](#how-do-i-mine-with-parity)
    * [Which chains are supported by Parity?](#which-chains-are-supported-by-parity)
    * [What are the different consensus engines available for Parity?](#what-are-the-different-consensus-engines-available-for-parity)
    * [How do I get Ether for the Morden, Ropsten, or Kovan testnets?](#how-do-i-get-ether-for-the-morden-ropsten-or-kovan-testnets)
    * [Does Parity provide a JavaScript console?](#does-parity-provide-a-javascript-console)
    * [None of my JSONRPC requests work, they all fail with no output!?!](#none-of-my-jsonrpc-requests-work-they-all-fail-with-no-output)
    * [When I start Parity with the 'homestead_test.json' I get a 0 blocks chain, and mining never seals a block!?!](#when-i-start-parity-with-the-homestead_testjson-i-get-a-0-blocks-chain-and-mining-never-seals-a-block)
    * [Why do my transactions not get mined?](#why-do-my-transactions-not-get-mined)
    * [All my 'eth_sendTransaction' calls only return '0x00000000...00000000' as return value!?!](#all-my-eth_sendtransaction-calls-only-return-0x0000000000000000-as-return-value)
 * [User Interface, Wallet and DApps](#user-interface-wallet-and-dapps)
    * [How can I access the Parity Wallet?](#how-can-i-access-the-parity-wallet)
    * [Can I use Mist with Parity?](#can-i-use-mist-with-parity)
    * [Can I send Bitcoin to my Parity wallet?](#can-i-send-bitcoin-to-my-parity-wallet)
    * [Can I create multi-signature wallets with Parity?](#can-i-create-multi-signature-wallets-with-parity)
    * [How can I compile and deploy contracts with Parity?](#how-can-i-compile-and-deploy-contracts-with-parity)
    * [How can I interact with existing contracts?](#how-can-i-interact-with-existing-contracts)
    * [What DApps are available for Parity?](#what-dapps-are-available-for-parity)
    * [How can I write a DApp for Parity?](#how-can-i-write-a-dapp-for-parity)
    * [Does Parity support the Swarm and Whisper sub-protocols?](#does-parity-support-the-swarm-and-whisper-sub-protocols)
 * [Troubleshooting (Yelp!!!!11)](#troubleshooting-yelp11)
    * [Double-clicking the Parity icon doesn't do anything!?!](#double-clicking-the-parity-icon-doesnt-do-anything)
    * [How can I make Parity to write logs?](#how-can-i-make-parity-to-write-logs)
    * [Where are the logs when Parity runs in daemon mode?](#where-are-the-logs-when-parity-runs-in-daemon-mode)
    * [How can I report an issue with Parity?](#how-can-i-report-an-issue-with-parity)
    * [I just scrolled all the way down here and didn't find what I'm looking for!?!](#i-just-scrolled-all-the-way-down-here-and-didnt-find-what-im-looking-for)


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

`@TODO @5chdn`

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

Parity supports standard Ethereum JSON-RPC interface for mining ([eth_getWork] (https://github.com/ethcore/parity/wiki/JSONRPC-eth-module#eth_getwork), [eth_submitWork](https://github.com/ethcore/parity/wiki/JSONRPC-eth-module#eth_submitwork) methods) and thus compatible with any miner which implements Ethereum Proof-of-Work.

First get a Parity node up and running (either build yourself or install one of the packages; the [[Setup]] guide can help you). Next, you'll need to install your preferred miner. Read more on [[Mining]].

#### Which chains are supported by Parity?

Parity supports multiple public chain configurations:

    --chain CHAIN                  Specify the blockchain type. CHAIN may be either a
                                   JSON chain specification file or olympic, frontier,
                                   homestead, mainnet, morden, ropsten, classic, expanse,
                                   testnet, kovan or dev (default: homestead).

- `--chain olympic` Runs the pre-release Ethereum Olympic testnet with network ID `0`.
- `--chain frontier` Runs the first released Ethereum public network with ID `1`. It does not include the Homestead and DAO hardforks.
- `--chain homestead` Runs the latest version of the Ethereum public network with ID `1`.
- `--chain mainnet` Same as `homestead`.
- `--chain foundation` Same as `homestead`.
- `--chain morden` Runs the first Ethereum public testnet with ID `2`, still used as Ethereum Classic public testnet.
- `--chain ropsten` Runs the second Ethereum public testnet with ID `3`.
- `--chain classic` Runs the Ethereum Classic public network which opposed the DAO hardfork.
- `--chain expanse` Runs the Expanse public network with ID `1`.
- `--chain kovan` Runs the Proof-of-Authority public testnet with ID `42`.
- `--chain testnet` Same as `kovan`.
- `--chain dev` Runs a private testnet configuration, see [[Private development chain]].

See also [[Chain specification]].

#### What are the different consensus engines available for Parity?

- Ethash (Ethereum Proof-of-Work Engine), see [Ethash](https://github.com/ethereum/wiki/wiki/Ethash).
- Instant Seal (Development Engine), see [[Private development chain]].
- Aura (Proof-of-Authority Validator Engine), see [[Aura]].
- Tendermint (Experimental Validator Engine), see [Validator Engines](https://github.com/ethcore/parity/wiki/Consensus-Engines#validator-engines)

#### How do I get Ether for the Morden, Ropsten, or Kovan testnets?

- See [Morden and Ropsten Faucets](http://ethereum.stackexchange.com/q/84).
- See [Kovan Faucets](https://github.com/kovan-testnet/faucet).

#### Does Parity provide a JavaScript console?

Yes, the Parity Wallet contains a Web3 console application. Navigate to the _'Applications'_ tab and open the _'Parity/Web3 Console'_.

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

Open your favorite browser and navigate to `localhost` on port `:8180`: http://127.0.0.1:8180/

You can change the port with the `--ui-port` flag. To disable the UI, use `--no-ui`.

#### Can I use Mist with Parity?

Yes, run `parity --geth` in Geth-compatibility mode. This sets the IPC path to be the same as Geth's and allows Mist to connect to Parity. See also [[Using Parity with Mist]].

#### Can I send Bitcoin to my Parity wallet?

No, sending Bitcoin directly to an Ethereum address does not work. However, you can use the Shapeshift integration. Open an account on your wallet and click the blue fox icon.

![Parity Shapeshift Integration](https://i.imgur.com/C7cAYeb.png)

#### Can I create multi-signature wallets with Parity?

Yes, open your wallet on the _A'ccounts'_ view and click _'New Wallet'_. This will deploy a multi-signature contract on the selected chain.

#### How can I compile and deploy contracts with Parity?

Open your Parity UI and navigate to the _'Contracts'_ tab, it allows you to write, compile and directly deploy contracts on the selected chain.

![Parity Solidity Contracts](https://i.imgur.com/2xjUkiI.png)

#### How can I interact with existing contracts?

`@TODO @5chdn`

#### What DApps are available for Parity?

Parity comes with a couple of builtin DApps:

- **Web 2.0 browser**: A Web 2.0 hosted pseudo-dapps browser.
- **Method registry**: A registry of method signatures for lookups on transactions.
- **Parity/Web3 console**: A Javascript development console complete with web3 and parity objects.
- **Registry**: A global registry of addresses on the network.
- **Token Deployment**: Deploy new basic tokens that you are able to send around.
- **Token Registry**: A registry of transactable tokens on the network.
- **TX-Queue Viewer**: Have a peak on internals of transaction queue of your node.

Additional DApps can be added, see [[Writing Dapps for Parity]].

#### How can I write a DApp for Parity?

Head over to the excellent [DApp Tutorial](https://github.com/ethcore/parity/wiki/Tutorial-Part-I) documentation. It walks you through making a simple Ethereum-powered, distributed app. By the end of it, you'll be able to head in to Parity, select your Dapp and see it in action.

#### Does Parity support the Swarm and Whisper sub-protocols?

Currently, Parity does neither support Swarm nor Whisper sub-protocols. However, [Whisper Support](https://github.com/ethcore/parity/issues/4685) is on the road-map.

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
