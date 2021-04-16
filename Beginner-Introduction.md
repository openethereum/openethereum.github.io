---
title: Beginner Introduction
---

This section shows the most usual configuration parameters for nodes users with practical examples. It can be used as an intro to better understand how to run OpenEthereum.

In the current [wiki](https://openethereum.github.io) more information on the client can be found such as all RPC calls and some explanations on the technology used.

## Most important options for beginners

For CLI or config parameters check `./openethereum --help` or visit [Configuring page](Configuring-OpenEthereum) and read the possible configurations of the client.

For all JSONRPC APIs visit: [JSONRPC API](JSONRPC)

## \-\-chain
For the the most basic usage, just start OpenEthereum with the default configuration `./openethereum`. This will be enough to sync into the Ethereum mainnet. You can choose other networks or testnets by specifying `--chain` (for example `--chain ropsten`). For more information about chains look at [Chain specification](Chain-specification)

## \-\-pruning
This option shows how much block history we are going to save. There are two types of nodes: **archive** and **pruning** nodes.
* **Archive** node has all of the block history saved in its database (It is needed at least +4TB of hard drive to fully sync on mainnet, and it  probably takes a few months to do it),
```bash
$ openethereum --pruning archive
```
*  **Pruning** (sometimes called **fast**) node is the default option. It removes old blocks and saves a configurable amount of blocks. For example, 64 newest blocks (this is configurable with `--pruning-history=[NUM]`). For hard disk usage check Etherscan live charts: https://etherscan.io/chartsync/chaindefault 

```bash
    --pruning=[METHOD]
        It configure pruning of the state/storage trie. METHOD options are archive, fast and auto: archive - it keeps full state
        trie data. No pruning. fast - it maintains journal overlay. Fast but 50MB used. auto - use the method most recently
        synced or default to fast if none synced. (default: auto)

    --pruning-history=[NUM]
        It sets a minimum number of recent states to keep in memory when pruning is active. (default: 64)

    --pruning-memory=[MB]
        The ideal amount of memory in megabytes to use to store recent states. As many states as possible will be kept
        within this limit, and at least --pruning-history states will always be kept. (default: 32)

```

## Warping (\-\-no-warp)
Warp is an additional feature that speeds sync up. It is a process where a state snapshot is downloaded at a particular block from other OE nodes and it takes it as the starting point. This procedure speeds up the initial syncing. It can be unreliable due to a low amount of peers and lack of nodes with snapshots. If this is the case and the node gets disconnected from the peer, there is not a mechanism to continue and the procedure must start again from the beginning. Even considering these inefficiencies , this is the fastest way to do a full sync.
A snapshot is made every 5000 blocks and it is dependant on what peer you are going to take it from. From a user: "In an example I got snapshoted to very low block `10300000` and it took a long time to sync to the highest block (2 days)." It can used `--warp-barrier` to state that it will only start warping on block snapshots that are newer than the specified number.

> Default values: warp is on (disable it with --no-warp). The creation of snapshot is by default off and it can enable it with (--periodic-snapshot)

Please read more on warping protocol in the following sections: [Warp Sync](Warp-Sync), [Snapshot Format](Warp-Sync-Snapshot-Format)


## Expected syncing time and hardware:
Currently (Oct 2020), this is an example of the expected syncing time using the code from the 3.1RC1 codebase without changes in the config on a high end server machine ( NVMe SSD, Intel Xeon, 62gb ram etc.) where the most important part of it is NVMe SSD because hard drive is the bottleneck of syncing.

| Timestamp | time diff | Stage | Description |
| -------- | -------- | -------- | -------- | 
| 2020-09-24 09:29:22     | +0     | Snapshot initializing     | Warping starts |
| 2020-09-24 10:26:52     | +00:57:30     | Syncing snapshot 5112/5112     | Warping finishes |
| 2020-09-24 10:26:57     | +00:00:05     | Syncing #10300002     | Syncing new blocks started |
| 2020-09-24 20:59:4     | +09:01:27     | Syncing #10400006     | Synced 100,000 blocks | 
| 2020-09-26 20:17:52     | +47:18:09     | Syncing #10900009     | Synced 500,000 blocks |

Warping usually lasts for ~1hour on this machine . For 100,000 it takes approximately around ~9 hours. You can check the full log [here](public/31rc1_mainnet_full_warp_sync.log)

Additionally, if syncing is started without warp and with pruning mode, the first few millions of blocks will be synced quickly but that speed will gradually decrease as it gets closer to the highest block and it can be expected a few blocks per seconds closer to top of the chain. This behaviour is expected.

## \-\-tracing
Trace logs are EVM output of transaction execution; they contain additional information on input/output/changes that transaction made to state. For traces to work it is needed to start the client with “./openethereum --tracing on” For more information on the content in trace follow this link: [JSONRPC-trace-module](JSONRPC-trace-module)

> Note: by default tracing is turned off

