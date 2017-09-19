### How do I run Parity?

After building Parity from source:

- On Windows run: `target/release/parity.exe`
- On MacOS and Linux run: `target/release/parity`

After installing Parity, just run `parity` from command line. See also [how do I configure Parity](https://github.com/paritytech/parity/wiki/FAQ#how-do-i-configure-parity) below.

### How do I configure Parity?

You can configure your Parity client by passing command-line flags to the executable. For usage instructions, see `parity --usage` and for a list of available flags, see `parity --help` or check out the [CLI Options here](https://github.com/paritytech/parity/wiki/Configuring-Parity#cli-options).

In additoin, Parity can be configured using a [TOML](https://github.com/toml-lang/toml) file. The file can be generated using the [Parity Config Generator](https://ethcore.github.io/parity-config-generator/). To start parity with a config file, the file needs to be located in:

* Windows: `%UserProfile%\AppData\Roaming\Parity\Ethereum\config.toml`
* Linux: `~/.local/share/io.parity.ethereum/config.toml`
* macOS: `$HOME/Library/Application Support/io.parity.ethereum/config.toml`

To use a custom path run `$ parity --config path/to/config.toml`. Read more on [Parity config file here](https://github.com/paritytech/parity/wiki/Configuring-Parity#config-file).

### Parity doesn't start on Windows, and fails with 'api-ms-win-crt-math-l1-1-0.dll' missing!?!

![parity_error](https://cloud.githubusercontent.com/assets/2982011/18855854/1d3b2c22-8424-11e6-8329-1dbe5edf6a5a.png)

Install the following windows update: https://support.microsoft.com/en-us/kb/2999226

### Parity uses 100% of disk I/O during sync

Parity needs high amount of disk usage, and by default your system will allow it to block other programs to access the disk while it's reading and writing. On Linux you can use [`ionice`](https://linux.die.net/man/1/ionice), and newer versions of Windows should allow you to navigate to the process (named "parity.exe") in Task Manager, right click, open the "Process priority" sub-menu and select "Background". If this menu doesn't appear, you may need to select "More details" at the bottom of the window. A WikiHow article with a description of this process on Windows 7 can be found [here](http://www.wikihow.com/Change-Process-Priorities-in-Windows-Task-Manager), and the process is much the same for newer versions of Windows.

### What are the different Parity synchronization and pruning modes?

Since Parity 1.2 state-trie pruning is enabled by default (`--pruning fast`). You can disable it by setting the pruning method to `archive` which keeps all state trie data:

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

### Why is warp synchronization so fast?

Warp sync extends previous versions of the protocol with full state snapshots. These snapshots can be used to quickly get a full copy of the state at a given block. Every 30,000 blocks, nodes will take a consensus-critical snapshot of that block's state. Any node can fetch these snapshots over the network, enabling a fast sync. These snapshots have been designed with out-of-order restoration in mind -- it isn't required to get any given chunk before another.

See also [what is Parity's “warp” sync](http://ethereum.stackexchange.com/q/9991) at Ethereum Stack Exchange and read [the full specification at the Wiki here](https://github.com/paritytech/parity/wiki/Warp-Sync-Snapshot-Format#manifest).

### How can I tell if Parity is fully synchronized?

To check whether Parity is currently synchronizing the chain, you can open the Web3 console and type:

```JavaScript
web3.eth.syncing
```

Returning `true` means the client is still busy fetching the chain. To get the latest block number, you can type:

```JavaScript
web3.eth.getBlock('latest').number
```

The Parity/Web3 console can be accessed via the UI in the 'Applications' tab.

### What can I do when Parity has trouble getting in sync?

Parity is running but seems to remain at the same block for a long time.

1. Make sure you have up to date version of Parity.
2. Go to http://time.is/ and ensure it says "Your time is exact":

   ![image](https://cloud.githubusercontent.com/assets/138296/19265409/5e4a89ce-8fa5-11e6-8ec6-6c72c138ee48.png)

   If it isn't get it synced. Your machine will not automatically do this; my machine is routinely 1-2 seconds out of sync which is enough to disrupt peer discovery.

3. Make sure your network does not block UDP traffic.
4. Remove your nodes file (`$HOME/.local/share/io.parity.ethereum/network/nodes.json`) while Parity is not running.
5. Wait a bit longer.

If the problem persists, please run Parity with `-l sync=trace` option for a while and post the output [here](https://github.com/paritytech/parity/issues/2447).

### Sync is stuck around block 2,421,000 or 2,675,000!?!

These blocks are quite heavy on the IO and take a long time to import, especially on machines with HDD. Consider using `--warp` or switching to SSD drive.

### Is there any Parity light client available?

The light client is not finished yet, but on the [road map for upcoming releases](https://github.com/paritytech/parity/projects/2).

### How do I enable transaction tracing (indexing)?

Transaction tracing, available in Parity 1.1 and above, enables all `CALL`/`CREATE` information (i.e. "internal transactions") to be efficiently searched and filtered. More information can be found on the dedicated [Wiki page](JSONRPC-trace-module).

### How do I create a new account?

Either by enabling the `personal` web3 API and typing

```JavaScript
web3.personal.newAccount()
```

in the Web3 console, or by using the Parity Wallet UI to create or import accounts:

![Parity Wallet Create New Account](https://i.imgur.com/Lnpczaa.png)

### How can I run Parity in Docker?

Docker images for Parity are available via [Docker Hub](Docker), i.e.:

```bash
docker pull ethcore/parity:beta
```

You can view the [Dockerfile here](https://github.com/paritytech/parity/blob/master/docker/ubuntu/Dockerfile).

### Can I run Parity as a daemon or system service?

The versions installed with the **Windows** and **MacOS X** installers automatically configure and enable a system service.

To manually enable the system service for Parity installed with **Homebrew**, run:

```bash
sudo launchctl enable io.parity.ethereum
sudo launchctl start io.parity.ethereum
```

To manually enable the system service for Parity installed on **Linux**, get the [`parity.service` file](https://github.com/paritytech/parity/blob/master/scripts/parity.service) from GitHub.

```bash
sudo wget https://raw.githubusercontent.com/paritytech/parity/master/scripts/parity.service -O /etc/systemd/system/parity.service
sudo chmod +x /etc/systemd/system/parity.service
sudo systemctl enable parity
sudo systemctl start parity
```

To manually run Parity in **daemon mode** in background, run `parity daemon`.

### How can I stop a Parity node cleanly?

If you run Parity in a terminal, hitting `CTRL+C` will cleanly exit it. Make sure to wait a couple of seconds.

To stop Parity started as system service, try to stop it with `systemctl` (Linux) or `launchctl` (MacOS):

```bash
sudo launchctl stop io.parity.ethereum
```

Another way, is to issue a _hang-up_ (HUP) call for the Parity client, i.e.:

```bash
killall -HUP parity
```
