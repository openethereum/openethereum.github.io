---
title: FAQ - Basic Operations, Configuration, and Synchronization
---

## How do I run Parity?

After building Parity from source:

- On Windows run: `target/release/parity.exe`
- On MacOS and Linux run: `target/release/parity`

After installing Parity, just run `parity` from command line. See also [how do I configure Parity](https://wiki.parity.io/FAQ#how-do-i-configure-parity) below.


## The UI isn't working when I visit 127.0.0.1:8180

Since Parity v1.10 the User Interface (UI) has been separated from the Parity Ethereum client. You can now access the User Interface through a separated application calles Parity UI. The browser UI has therefore been disabled by default. Please follow [these instructions](https://wiki.parity.io/Parity-Wallet) to download and user Parity wallet using the Parity UI app.

## The Parity UI application isn't working the way I want

Please report any bug or unexpected behavior by [creating an issue in GitHub Parity UI repository](https://github.com/Parity-JS/shell/issues/new).
If you still want to use the broswer UI (deprecated) you can launch Parity Ethereum Client with the flag `--force-ui` and visit 127.0.0.1:8180 in your favorit browser.

## How do I configure Parity?

You can configure your Parity client by passing command-line flags to the executable. For usage instructions, see `parity --usage` and for a list of available flags, see `parity --help` or check out the [CLI Options here](https://wiki.parity.io/Configuring-Parity#cli-options).

In additoin, Parity can be configured using a [TOML](https://github.com/toml-lang/toml) file. The file can be generated using the [Parity Config Generator](https://ethcore.github.io/parity-config-generator/). To start parity with a config file, the file needs to be located in:

* Windows: `%UserProfile%\AppData\Roaming\Parity\Ethereum\config.toml`
* Linux: `~/.local/share/io.parity.ethereum/config.toml`
* macOS: `$HOME/Library/Application Support/io.parity.ethereum/config.toml`

To use a custom path run `$ parity --config path/to/config.toml`. Read more on [Parity config file here](https://wiki.parity.io/Configuring-Parity#config-file).

## Parity doesn't start on Windows, and fails with 'api-ms-win-crt-math-l1-1-0.dll' missing!?!

![parity_error](https://cloud.githubusercontent.com/assets/2982011/18855854/1d3b2c22-8424-11e6-8329-1dbe5edf6a5a.png)

Install the following windows update: https://support.microsoft.com/en-us/kb/2999226

## Parity uses 100% of disk I/O during sync

Parity needs high amount of disk usage, and by default your system will allow it to block other programs to access the disk while it's reading and writing. On Linux you can use [`ionice`](https://linux.die.net/man/1/ionice), and newer versions of Windows should allow you to navigate to the process (named "parity.exe") in Task Manager, right click, open the "Process priority" sub-menu and select "Background". If this menu doesn't appear, you may need to select "More details" at the bottom of the window. A WikiHow article with a description of this process on Windows 7 can be found [here](http://www.wikihow.com/Change-Process-Priorities-in-Windows-Task-Manager), and the process is much the same for newer versions of Windows.

## What are the different Parity synchronization and pruning modes?

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

## Why is warp synchronization so fast?

Warp sync extends previous versions of the protocol with full state snapshots. These snapshots can be used to quickly get a full copy of the state at a given block. Every 5,000 blocks, nodes will take a consensus-critical snapshot of that block's state. Any node can fetch these snapshots over the network, enabling a fast sync. These snapshots have been designed with out-of-order restoration in mind -- it isn't required to get any given chunk before another.

To make sure you get the most recent snapshot available and warp sync very close to the top of the chain, Parity >=v1.10.1 supports the flag `--warp-barrier [NUM]`. Replace [NUM] with [the chain's latest block number](https://stats.parity.io/) **minus at least 10,000 blocks**. Example: if latest block number is 5,432,123 you should launch `parity --warp-barrier 5420000`.


See also [what is Parity's “warp” sync](http://ethereum.stackexchange.com/q/9991) at Ethereum Stack Exchange and read [the full specification at the Wiki here](https://wiki.parity.io/Warp-Sync-Snapshot-Format#manifest).

## How can I tell if Parity is fully synchronized?

To check whether Parity is currently synchronizing the chain, you can open the Web3 console and type:

```JavaScript
web3.eth.syncing
```

Returning `true` means the client is still busy fetching the chain. To get the latest block number, you can type:

```JavaScript
web3.eth.getBlock('latest').number
```

The Parity/Web3 console can be accessed via the UI in the 'Applications' tab.

## What can I do when Parity has trouble getting in sync?

Parity is running but seems to remain at the same block for a long time.

1. Make sure you have the latest version of Parity.
2. Go to http://time.is/ and ensure it says "Your time is exact":

   ![image](https://cloud.githubusercontent.com/assets/138296/19265409/5e4a89ce-8fa5-11e6-8ec6-6c72c138ee48.png)

   If it isn't get it synced. Your machine will not automatically do this; my machine is routinely 1-2 seconds out of sync which is enough to disrupt peer discovery.

3. Make sure your network does not block UDP traffic.
4. Remove your nodes file (`$HOME/.local/share/io.parity.ethereum/network/nodes.json`) while Parity is not running.
5. Wait a bit longer.

If the problem persists, please run Parity with `-l sync=trace` option for a while and post the output [here](https://github.com/paritytech/parity/issues/2447).

## Sync is stuck around block 2,421,000 or 2,675,000!?!

These blocks are quite heavy on the IO and take a long time to import, especially on machines with HDD. Consider using `--warp` or switching to SSD drive.

## Is there any Parity light client available?

The light client is not finished yet, but on the [road map for upcoming releases](https://github.com/paritytech/parity/projects/2).

## How do I enable transaction tracing (indexing)?

Transaction tracing, available in Parity 1.1 and above, enables all `CALL`/`CREATE` information (i.e. "internal transactions") to be efficiently searched and filtered. More information can be found on the dedicated [Wiki page](JSONRPC-trace-module).

## How do I create a new account?

Either by enabling the `personal` web3 API and typing

```JavaScript
web3.personal.newAccount()
```

in the Web3 console, or by using the Parity Wallet UI to create or import accounts:

![Parity Wallet Create New Account](https://i.imgur.com/Lnpczaa.png)

## How can I run Parity in Docker?

Docker images for Parity are available via [Docker Hub](Docker), i.e.:

```bash
docker pull ethcore/parity:beta
```

You can view the [Dockerfile here](https://github.com/paritytech/parity/blob/master/docker/ubuntu/Dockerfile).

## Can I run Parity as a daemon or system service?

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

## How can I stop a Parity node cleanly?

If you run Parity in a terminal, hitting `CTRL+C` will cleanly exit it. Make sure to wait a couple of seconds.

To stop Parity started as system service, try to stop it with `systemctl` (Linux) or `launchctl` (MacOS):

```bash
sudo launchctl stop io.parity.ethereum
```

Another way, is to issue a _hang-up_ (HUP) call for the Parity client, i.e.:

```bash
killall -HUP parity
```

## How to launch Parity using the command line on Windows?

1. Make sure you close parity and that no parity process is running (using <kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>Esc</kbd>)
1. Navigate in Windows Explorer to the Parity folder where parity.exe is, it should be located in `C:\Program Files\Parity Technologies\Parity`
1. Highlight the complete folder path in the top pane, type `cmd` and hit Enter
![windows pane](/images/windows-explorer-cmd.jpeg)
1. This opens a command line window in the Parity folder, you can launch parity with [CLI flags](https://wiki.parity.io/Configuring-Parity.html#cli-options)

## How to launch Parity using the command line on Mac OSX?

1. Make sure you close parity and that no parity process is running. ([See here how to kill a process](http://osxdaily.com/2012/03/02/force-quit-mac-apps/))
1. Press <kbd>CMD</kbd> + <kbd>Space</kbd> to open spotlight search
1. Type `terminal` and hit return to open Terminal app.
1. Type or paste `/Applications/Parity\ Ethereum.app/Contents/MacOS/parity` in the terminal, you can launch parity with [CLI flags](https://wiki.parity.io/Configuring-Parity.html#cli-options) if needed.


## How to generate a new hardcoded sync block for Parity light client?

Parity light client allows you to set a hardcoded block from which the light client will sync to reach the top of the chain faster. Note that this hardcoded block will only be used if no prior light database is found. Here is how you can generate the needed info to create a chain specifications file:
1. Run `parity --light` and make sure to reach the top of the chain.
1. Stop parity with `ctrl-c`.
1. Run `parity export-hardcoded-sync`.
1. Copy the output of the previous command using the field : `"hardcodedSync": { COPY_OUTPUT_HERE }` into your new [chain specifications](https://wiki.parity.io/Chain-specification.html) file.
1. Run the light client with your new specificatiions `parity --light --chain /path/to/your/new/specifications`

