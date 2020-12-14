---
title: Frequently Asked Questions
libs:
  faq: true
---

## Summary
- [Most asked questions](#most-asked-questions)
- [Backup, Restore and Files](#backup-restore-and-files)
- [Basic Operations, Configuration, and Synchronization](#basic-operations-configuration-and-synchronization)
- [CLI, Mining, and Networks](#cli-mining-and-networks)
- [Troubleshooting](#troubleshooting)


## Most asked questions
### How to get OpenEthereum in sync quickly?
The quickest way of syncing OpenEthereum client with the top of the chain is achieved using Warp Sync in combination with `warp-barrier`.

To make sure you get the most recent snapshot available and warp sync very close to the top of the chain, OpenEthereum supports the flag `--warp-barrier [NUM]`. Replace [NUM] with [the chain's latest block number](https://etherscan.io) **minus at least 10,000 blocks**. Example: if latest block number is 5,633,123 you should launch `openethereum --warp-barrier 5620000`.

Read more about [Warp Sync](Warp-Sync-Snapshot-Format).

### What are the OpenEthereum disk space needs and overall hardware requirements?
Running a node with the standard configuration for the Ethereum Mainnet requires a lot of computer resources. The blockchain download and validation process are particularly heavy on CPU and disk IO. It is therefore recommended to run a full node on a computer with multi-core CPU, 8GB RAM and an SSD drive and at least 400GB free space. Internet connection can also be a limiting factor. A decent DSL connection is required.

Node performance can also be improved significantly by using OpenEthereum's caching features, and accessing state from memory instead of from your disk by specifying the `--cache-size` flag - optimally this requires you to have additional RAM equivalent to the state size of the blockchain you are connected to, over 8GB on Ethereum mainnet, for a total of over 12GB of RAM.

#### Storage Requirements Overview
Indicative data storage requirement from December 2018 syncing Ethereum Mainnet with OpenEthereum 2.2.4:
```
Client / Mode                  | Block Number   | Disk Space | CLI flags                    |
===============================|================|============|==============================|
openethereum +warp -ancient    |      6_850_000 |        29G | --no-ancient-blocks          |
openethereum +warp             |      6_850_000 |       133G |                              |
openethereum -warp +archive    |      6_850_000 |       1.8T | --pruning archive            |
```

### What does OpenEthereum's command line output mean?
When running OpenEthereum using the command line, a lot of information is displayed in the terminal. Here is a description of these logs.
#### While syncing the full node
Example: `2020-12-16 16:32:22 UTC Syncing #11464865 0x2ead…34be 2.00 blk/s  452.2 tx/s 24.9 Mgas/s 0+ 332 Qed Ancient:#10268069 LI:#11465198 38/50 peers 5 MiB chain 61 MiB queue  RPC: 0 conn,  0 req/s, 0 µs`
- `2020-12-16 16:32:22`: timestamp of the log
- `Syncing #11464865`: Newest block's number
- `0x2ead…34be`: Newest block's hash
- `2.00 blk/s`: average block(s) processed per second
- `452.2 tx/s`: average transactions processed per second
- `24.9 Mgas/s`: million gas processed per second
- `0+`: unverified block queue size
- `332 Qed`: Queueed blocks for execution.
- `Ancient:#10268069` Best ancient block. Only visible if we warped. Will dissapear when all ancient blocks are inserted.
- `LI:#11465198`: Last inserted block number.
- `25/25 peers`: number of active peers  / configured maximum number of peers
- `128 MiB chain`: blockchain cache info memory used
- `5 MiB queue`: queue memory used (contains information about the queued blocks)
- `RPC 0 conn`: number of RPC connections to the node
- `0 req/s`: number of RPC request per seconds
- `0 µs`: average RPC roundtrip time for a single request

#### Imported blocks once the node is synced with the top of the chain
Example: `2020-12-16 17:49:28 UTC Imported #11465619 0x1e4e…016f (239 txs, 12.41 Mgas, 631 ms, 42.82 KiB) + another 1 block(s) containing 132 tx(s)`
- `2020-12-16 17:49:28 UTC`: timestamp of the log
- `Imported #11465619`: block number received from peers
- `0x1e4e…016f`: block's truncated hash
- `239 txs`: number of transactions in the block
- `12.41 Mgas`: million gas used in the imported block
- `631 ms`: the time it took to process the block
- `42.82 KiB`: block size
- `+ another 1 block(s) containing 132 tx(s)`: appears when 2 or more blocks are imported within 1 tick of informant (one line in the console) - In that particular example it means that 2 blocks were imported since last `Imported` line and the second had 132 transactions.

#### Verifying blocks after warp sync
Example: `2020-12-16 17:58:54 UTC  Ancient:#11199806 AB:#11199806   42/50 peers   744 bytes chain 0 bytes queue  RPC:  0 conn,    0 req/s,    0 µs`

Warp Sync allows the node download a snapshot of the state of the blockchain at a block number close to the top of the chain. It allows the node to get in sync with the top of the chain quickly. Nevertheless, a full node needs to verify every block from the genesis. This process is done in parallel with the sync. This line starting with a block number means that the node is currently processing and verifying an older block.

- `2020-12-16 17:58:54 UTC`: timestamp of the log
- `Ancient:#11199806`: Best ancient block's number
- `AB:#11199806`: Ancient block currently queued.
- `42/50 peers`: number of active peers  / configured maximum number of peers
- `744 bytes chain`: blockchain cache info memory used
- `0 bytes queue`: queue memory used (contains information about the queued blocks)
- `24 MiB sync`: sync memory used (contains information about the connected peers, last imported block, etc.)
- `RPC 0 conn`: number of RPC connections to the node
- `0 req/s`: number of RPC request per seconds
- `0 µs`: average RPC roundtrip time for a single request


### How to contribute to this wiki documentation?

This Wiki is open source, anybody can contribute! If you spot an error, a typo, or feel like creating or amending some content, feel free to do so. Here is a quick how-to:

1. Visit [https://github.com/openethereum/wiki](https://github.com/openethereum/wiki) repository and click on `Fork` in the top right-hand corner.  
![Fork on github](/images/wiki-contribute-0.jpg)
1. The Wiki will be cloned in your own repo, you will be redirected to https://github.com/your_handle/wiki
1. From here you can edit a page directly by clicking on a file and edit it (click on the pen on the top right-hand corner)
1. Once you are done editing, describe and commit your changes in your master branch  
![Commit to master](/images/wiki-contribute-1.jpg)
1. You will now be able to submit a Pull Request (PR) to the OpenEthereum Wiki repo.
1. Click on the `Pull Requests` and then on the `New Pull Request` button  
![Pull Request](/images/wiki-contribute-2.jpg)
1. From here, you are able to select what branch of your repo should be merged to what branch on OpenEthereum Wiki repo  
![Merge into branch](/images/wiki-contribute-3.jpg)
1. The default option will propose to merge from your master branch to the OpenEthereum Wiki's master branch, which is fine.
1. Double check the modifications you made and click on `Create a Pull Request` when you are sure  
1. Describe your changes and submit it!

## Backup, Restore and Files

### Where can I find OpenEthereum's local files?

On Windows blockchain data is located in `C:\Users\You\AppData\Local\OpenEthereum`
while keys and configuration are kept in `C:\Users\You\AppData\Roaming\OpenEthereum`

On macOS: `/Users/you/Library/Application Support/OpenEthereum`

On Linux: `/home/you/.local/share/openethereum`

### How can I backup my accounts and keys? How can I restore my keys?

See [Backing-up-&-Restoring](Backing-up-&-Restoring).

### Can I import my keys from Geth?

They get imported automatically. :)

### Can I import accounts not created with OpenEthereum or Geth (e.g., MyEtherWallet)?

Copy your keyfiles into your `$HOME/.local/share/openethereum/keys` directory. OpenEthereum will import all keys found there.

### How do I backup my blockchain?

You can just export it to a file:

```bash
openethereum export blocks $HOME/ethereum-chain-backup.rlp
```

### How do I restore my blockchain from a previous backup?

Just use `import`:
```bash
openethereum import $HOME/ethereum-chain-backup.rlp
```

### How to delete the blockchain to initiate full re-sync without deleting wallet info?

- Stop OpenEthereum, then execute `openethereum db kill`.
- Relevant options are `--chain=*` `--pruning=*` and `--db-path/--base-path`, i.e. to delete `goerli` run:

```bash
openethereum db kill --chain=goerli
```

## Basic Operations, Configuration, and Synchronization

### How do I run OpenEthereum?

After installing OpenEthereum, just run `openethereum` from the command line. See also [how do I configure OpenEthereum](#how-do-i-configure-openethereum) below

After building OpenEthereum from source:

- On Windows run: `target/release/openethereum.exe`
- On macOS and Linux run: `target/release/openethereum`

### How do I configure OpenEthereum?

You can configure your OpenEthereum client by passing command-line flags to the executable. For usage instructions, see `openethereum --usage` and for a list of available flags, see `openethereum --help` or check out the [CLI Options here](Configuring-OpenEthereum#cli-options).

In addition, OpenEthereum can be configured using a [TOML](https://github.com/toml-lang/toml) file. To start OpenEthereum with a config file, the file needs to be located in:

* Windows: `%UserProfile%\AppData\Roaming\OpenEthereum\config.toml`
* Linux: `~/.local/share/openethereum/config.toml`
* macOS: `$HOME/Library/Application Support/OpenEthereum/config.toml`

To use a custom path run `$ openethereum --config path/to/config.toml`. Read more on [OpenEthereum config file here](Configuring-OpenEthereum#config-file).

### What are the security best practices?

- Prefer [personal_sendTransaction](JSONRPC-personal-module#personal_sendtransaction) over unlockAccount or the `--unlock` CLI flag to securely send transactions without leaving an account unlocked.
- Do not use `all`/`0.0.0.0` for `--ws-interface`, `--ws-hosts`, `--ws-origins` and prefer a proper IP address/domain.
- Do not use `all`/`0.0.0.0` for `--jsonrpc-interface`, `--jsonrpc-hosts`, `--jsonrpc-origins` and prefer a proper IP address/domain.
- Do not use `*`/`all` for`--jsonrpc-cors`, `--ipfs-api-cors` and prefer a proper domain.
- Do not use `--unsafe-expose` on a production server.
- To access a server [SSH tunneling](Wallet-Remote-Access) is the way to go.
- Regular users should not use `--unlock`.
- Limit the `--jsonrpc-apis` if you don't need everything.
- Disable unused servers: `--no-ws` `--no-ipc`.

### What are the different OpenEthereum synchronization and pruning modes?

Since OpenEthereum v1.2, state-trie pruning is enabled by default (`--pruning fast`). You can disable it by setting the pruning method to `archive` which keeps all state trie data:

    --pruning METHOD               Configure pruning of the state/storage trie. METHOD
                                   may be one of auto, archive, fast:
                                   archive - keep all state trie data. No pruning.
                                   fast - maintain journal overlay. Fast but 50MB used.
                                   auto - use the method most recently synced or
                                   default to fast if none synced (default: auto).

To reduce the size of the kept pruning history, you can set the minimum number of recent states with the `--pruning-history` flag:

    --pruning-history NUM          Set a minimum number of recent states to keep when pruning
                                   is active. (default: 64).
    --pruning-memory MB            The ideal amount of memory in megabytes to use to store
                                   recent states. As many states as possible will be kept
                                   within this limit, and at least --pruning-history states
                                   will always be kept. (default: 32)


By default, 64 states are kept.

### What can I do when OpenEthereum has trouble getting in sync?

OpenEthereum is running but seems to remain at the same block for a long time.

1. Make sure you have the latest version of the client.
2. Go to http://time.is/ and ensure it says "Your time is exact":

   ![your time is exact](images/time_is.jpg)

   If it isn't, get it synced. Your machine will not automatically do this; my machine is routinely 1-2 seconds out of sync which is enough to disrupt peer discovery.

3. Make sure your network does not block UDP traffic.
4. Remove your nodes file (`$HOME/.local/share/openethereum/network/nodes.json`) while OpenEthereum is not running.
5. Wait a bit longer.

If the problem persists, please run OpenEthereum with `-l sync=trace` option for a while and post the output [here](https://github.com/openethereum/openethereum/issues/2447).

### Sync is stuck around block 2,421,000 or 2,675,000!?!

These blocks are quite heavy on the IO and take a long time to import, especially on machines with HDD. Consider using `--warp` or switching to SSD drive.

### How can I run OpenEthereum in Docker?

Docker images for OpenEthereum are available via [Docker Hub](Docker), i.e.:

```bash
docker pull openethereum/openethereum
```

You can view the [Dockerfile here](https://github.com/openethereum/openethereum/blob/master/docker/ubuntu/Dockerfile).

### Can I run OpenEthereum as a daemon or system service?

The versions installed with the **Windows** and **macOS** installers automatically configure and enable a system service.

To manually enable the system service for OpenEthereum installed with **Homebrew**, run:

```bash
sudo launchctl enable openethereum
sudo launchctl start openethereum
```

To manually enable the system service for OpenEthereum installed on **Linux**, get the [`openethereum.service` file](https://github.com/openethereum/openethereum/blob/master/scripts/openethereum.service) from GitHub.

```bash
sudo wget https://raw.githubusercontent.com/openethereum/openethereum/master/scripts/openethereum.service -O /etc/systemd/system/openethereum.service
sudo chmod +x /etc/systemd/system/openethereum.service
sudo systemctl enable openethereum
sudo systemctl start openethereum
```

### How can I stop a OpenEthereum node cleanly?

If you run OpenEthereum in a terminal, hitting `CTRL+C` will cleanly exit it. Make sure to wait a couple of seconds.

To stop OpenEthereum started as system service, try to stop it with `systemctl` (Linux) or `launchctl` (macOS):

```bash
sudo launchctl stop openethereum
```

Another way is to issue a _hang-up_ (HUP) call for the OpenEthereum client, i.e.:

```bash
killall -HUP openethereum
```

### How to launch OpenEthereum using the command line on Windows?

1. Make sure you close OpenEthereum and that no `openethereum` process is running (using <kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>Esc</kbd>)
1. Navigate in Windows Explorer to the OpenEthereum folder where openethereum.exe is, it should be located in `C:\Program Files\OpenEthereum`
1. Highlight the complete folder path in the top pane, type `cmd` and hit Enter
![windows pane](/images/windows-explorer-cmd.jpeg)
1. This opens a command line window in the OpenEthereum folder, you can launch OpenEthereum with [CLI flags](Configuring-OpenEthereum#cli-options)

### How to launch OpenEthereum using the command line on Mac OSX?

1. Make sure you close OpenEthereum and that no `openethereum` process is running. ([See here how to kill a process](http://osxdaily.com/2012/03/02/force-quit-mac-apps/))
1. Press <kbd>CMD</kbd> + <kbd>Space</kbd> to open spotlight search
1. Type `terminal` and hit return to open Terminal app.
1. Type or paste `/Applications/OpenEthereum.app/Contents/MacOS/openethereum` in the terminal, you can launch OpenEthereum with [CLI flags](Configuring-OpenEthereum#cli-options) if needed.

## CLI, Mining, and Networks

### How do I mine with OpenEthereum?

OpenEthereum supports standard Ethereum JSON-RPC interface for mining ([eth_getWork](JSONRPC-eth-module#eth_getwork), [eth_submitWork](JSONRPC-eth-module#eth_submitwork) methods) and thus compatible with any miner which implements Ethereum Proof-of-Work.

First get a OpenEthereum node up and running (either build yourself or install one of the packages; the [Setup](Setup) guide can help you). Next, you'll need to install your preferred miner. Read more on [Mining](Mining).


### What are the different consensus engines available for OpenEthereum?
- Ethash (Ethereum Proof-of-Work Engine), see [Ethash](https://github.com/ethereum/wiki/wiki/Ethash).
- Instant Seal (Development Engine), see [Private development chain](Private-development-chain).
- Aura (Proof-of-Authority Validator Engine), see [Aura](Aura).
- Clique (Proof-of-Authority Validator Engine).

### How do I get Ether for the Görli, Rinkeby or Kovan testnets?
- See [Görli Authenticated Faucet](https://goerli.net).
- See [Rinkeby Authenticated Faucet](https://faucet.rinkeby.io).
- See [Kovan Faucets](https://github.com/kovan-testnet/faucet).

### None of my JSONRPC requests work, they all fail with no output!?!
As a safeguard against a certain type of attack, OpenEthereum requires you to use a special content type. Just add `-H "Content-Type: application/json"` to your `curl` command.

### Why do my transactions not get mined?
Your gas price is probably too low. Try increasing the gas price to 20Gwei by appending `, gasPrice: 20000000000` into your transaction spec or `sendTransaction` options.

### All my 'eth_sendTransaction' calls only return '0x00000000...00000000' as return value!?!
You're probably trying to send from a locked account. Ensure you use the `--unlock` (to specify which accounts to unlock) and `--password` (to pass in the file contain any passwords needed to unlock those accounts) so that the accounts from which you're sending the transaction are unlocked.

### I've received an abuse message from my ISP/hosting provider. Something about malicious activity triggered by OpenEthereum.
See [Network Configuration](Network-Configuration) for some tips on how to configure OpenEthereum to behave.

### Does OpenEthereum support the Swarm and Whisper sub-protocols?
No.

## Troubleshooting

### Double-clicking the OpenEthereum icon doesn't do anything!?!
Probably your node is already running (or another node is running, i.e., Geth) and another instance of OpenEthereum cannot be launched with the same configuration (ports, dapps, web interface, etc.).

### How can I make OpenEthereum write logs?

You can control the logging level with `-l` and define a `--log_file` location:

```bash
  -l --logging LOGGING           Specify the general logging level (error, warn, info, debug or trace).
                                 It can also be set for a specific module. Example: '-l sync=debug, rpc=trace'.
  --log-file FILENAME            Specify a filename into which logging should be
                                 appended. (default: None)
```
LOGGING accepts the following modules:
`account_bloom`,`basicauthority`,`blockchain`,`chain`,`client`,`cull`,`dapps`,`discovery`,`diskmap`,`enact`,`engine`,`estimate_gas`,`ethash`,`evm`,`executive`,`ext`,`externalities`,`external_tx`,`fatdb`,`fetch`,`finality`,`hw`,`hypervisor`,`import`,`jdb`,`jdb.fine`,`jdb.ops`,`journaldb`,`jsontests`,`les`,`light_fetch`,`local_store`,`migration`,`miner`,`mode`,`network`,`on_demand`,`own_tx`, `perf`,`pip`,`pip_provider`,`poa`,`pubsub`,`rcdb`,`reorg`,`rpc`,`rpc-client`,`secretstore`,`secretstore_net`,`shutdown`,`signer`,`snapshot`,`snapshot_io`,`snapshot_watcher`,`spec`,`state`,`stats`,`stratum`,`sync`,`trace`,`tx`,`tx_filter`,`txpool`,`txqueue`,`updater`,`verification`,`warp`,`wasm`

Additionally, a logging level can  be set per module:
`info`, `debug`, `trace` (default if no level is specified for a module)

Examples:
- `-l sync=debug` prints the debug level logs for the sync module only.
- `-l sync,snapshot,txqueue` prints the logs for sync, snapshot, and txqueue with trace level.
- `-l sync=info,snapshot=debug,txqueue=trace` prints the info level logs for the sync module, the debug level of logs for the snapshot module etc.

Note that the info logging level of all modules will always be printed, equivalent to `-l info`.

See also: [How to make OpenEthereum write logs](http://ethereum.stackexchange.com/questions/3331/how-to-make-parity-write-logs)?

### Where are the logs when OpenEthereum runs in daemon mode?

Use

```bash
openethereum daemon --log-file /path/to/openethereum.log
```

See also: [Where are the OpenEthereum log files in daemon mode](http://ethereum.stackexchange.com/questions/11363/where-are-the-parity-log-files-in-daemon-mode)?


### OpenEthereum uses 100% of disk I/O during sync

OpenEthereum is a disk IO intensive application. By default, your system will allow it to block other programs to access the disk while it's reading and writing. On Linux, you can use [`ionice`](https://linux.die.net/man/1/ionice), and newer versions of Windows should allow you to navigate to the process (named "openethereum.exe") in Task Manager, right click, open the "Process priority" sub-menu and select "Background". If this menu doesn't appear, you may need to select "More details" at the bottom of the window. A WikiHow article with a description of this process on Windows 7 can be found [here](http://www.wikihow.com/Change-Process-Priorities-in-Windows-Task-Manager), and the process is much the same for newer versions of Windows.

### How can I report an issue with OpenEthereum?

If you have any urgent matters, get in [touch with us on Gitter](https://gitter.im/openethereum/openethereum). If you run into issues with OpenEthereum, please consider [creating a ticket on Github](https://github.com/openethereum/openethereum/issues/new).



## Building, Installing and Testing 
### Are there any installers available?
You can download the latest versions from the [releases page on GitHub](https://github.com/openethereum/openethereum/releases).

### Where can I find official releases?
You can download the latest versions from the [releases page on GitHub](https://github.com/openethereum/openethereum/releases). :)

Make sure you check the `sha256sum`, on your command-line, type

```bash
sha256sum openethereum-linux-v3.0.0.zip
```

and compare the output with the provided checksum at the [releases page on GitHub](https://github.com/openethereum/openethereum/releases).

```bash
202b14eb8c3ec0bb281293b37668427a1e627ee55d221a0326814e35209419d3 openethereum-linux-v3.0.0.zip
```

### Are builds for ARM devices available (i.e., Raspberry Pi)?

There are builds for ARMhf (ARMv7) and ARM64 on our releases page on GitHub.

In addition, The [EthRaspbian](https://github.com/diglos/pi-gen) project provides Debian-flavored images for the Raspberry Pi which includes OpenEthereum as default client. In addition, the [EthArmbian](https://github.com/diglos/userpatches) project supports Odroid C2 devices.

### How do I install OpenEthereum after building?
- On Windows: `copy target/release/openethereum.exe C:/Windows`
- On Mac/Linux: `cp -f target/release/openethereum /usr/local/bin`

### How do I update OpenEthereum?
Follow the instructions above to download (or build) and install. The new version will replace the old. Nothing additional is needed.

### I'm getting build errors, what can I do?
Make sure you are using the latest stable rust compiler. Update with `rustup update` when using rustup.

### I'm trying to build but get 'libssl' errors!?!
Install required packages `libssl-dev`.

### I'm trying to build on Windows but I get 'link failed' errors mentioning 'kernel32.lib'!?!
Make sure you have set up your 64-bit variables in the build shell before issuing `cargo build`:

````
"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64\vcvars64.bat"
````

### How can I cross-build OpenEthereum for other platforms (e.g., ARM)?
See the documentation for [docker build for ARM](https://github.com/paritytech/parity-snappy/wiki/Docker-build-for-ARM-ARM64).

### How can I build and run tests?
First, make sure you have the cross-client consensus tests installed:

```
git submodule init
git submodule update
```

Then you just run `./test.sh`. You can run individual tests with `cargo test -p <package-name>`.

### I just scrolled all the way down here and didn't find what I'm looking for!?!

Get in [touch with us on Discord](https://discord.io/openethereum). If you run into issues with OpenEthereum, please consider [creating a ticket on Github](https://github.com/openethereum/openethereum/issues/new)
