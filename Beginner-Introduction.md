---
title: Beginner Introduction
---

With this section We want to bridge that gap and give you a small intro that you can read to better understand how to run OpenEthereum and what to expect.

## Full information
Important options that new users need to know at beginning:
In current [wiki](https://openethereum.github.io) you can find more information on the client such as all RPC calls and some explanation on technology that we use. 

For CLI or config parameters you can check `./openethereum --help` or visit [Configuring page](Configuring-OpenEthereum) and read all of the possible configurations of the client.

For all JSONRPC APIs you can visit this: [JSONRPC API](JSONRPC)

## \-\-chain
For the most basic usage of application you can just start OpenEthereum with default configuration `./openethereum` and this will be enough to sync onto Ethereum mainnet. You can choose another network or testnet by specifying `--chain` (for example `--chain ropsten`). About more information chains you can look at [Chain specification](Chain-specification)

## \-\-pruning
This option tells us how much blocks history are we going to save. There are two types of nodes: **archive** and **pruning** nodes.
* **Archive** node is node that has all block history saved in its database (you will need at least 4TB of harddisk to fully sync on mainnet, and it will take you few months to do it),
```bash
$ openethereum --pruning archive
```
* while **Pruning** (or it is somewhere called **fast**) is default option and what it does it prunes old blocks and saves only for example 64 newest blocks (this is configurable with `--pruning-history=[NUM]`). For hard disk usage you can see how much etherscan takes: https://etherscan.io/chartsync/chaindefault . For me it was in range of ~420gb with tracing enabled.

```bash
    --pruning=[METHOD]
        Configure pruning of the state/storage trie. METHOD may be one of auto, archive, fast: archive - keep all state
        trie data. No pruning. fast - maintain journal overlay. Fast but 50MB used. auto - use the method most recently
        synced or default to fast if none synced. (default: auto)

    --pruning-history=[NUM]
        Set a minimum number of recent states to keep in memory when pruning is active. (default: 64)

    --pruning-memory=[MB]
        The ideal amount of memory in megabytes to use to store recent states. As many states as possible will be kept
        within this limit, and at least --pruning-history states will always be kept. (default: 32)

```

## Warping (\-\-no-warp)
For the time needed to do fast sync, it still depends on options used. There is an additional feature in OE that speeds it up and that is warp. Warp is a process where we download a state snapshot, at a particular block, from other OE nodes and take that as our starting point, with this we are speeding up the process of initial syncing. This process can be unreliable in tearms is it going to finish or not for reasons that there are not a lot of OE with snapshots and there is a limit on how many peers you can sync from one peer, and it can happen that you just get disconnected from peer you were warping from and there is no mechanism to continue downloading, you need to start from beginning again. But either way it is the fastest way to do full sync.
Snapshot is made every 5000 blocks and when you do warping it depends on what peer are you going to take snapshot from. In my example I got snapshoted to very low block `10300000` and it took a long time to sync to the highest block (2 days). You can use `--warp-barrier` to say that you will only start warping on block snapshots that are newer than the specified number.

> Default values: warp is on (disable it with --no-warp). Creating of snapshot is by default off and you can enable it with (--periodic-snapshot)

You can read more on warping protocol in sections: [Warp Sync](Warp-Sync), [Snapshot Format](Warp-Sync-Snapshot-Format)

## Expected time and hardware:
Now, expected syncing time with code from 3.1RC1 codebase with no changes in config on high end server machine ( NVMe SSD, Intel Xeon, 62gb ram etc.) where most important part of it is NVMe SSD because hard disk is the bottleneck of syncing, is for rough estimation around:

| Timestamp | time diff | Stage | Description |
| -------- | -------- | -------- | -------- | 
| 2020-09-24 09:29:22     | +0     | Snapshot initializing     | Warping starts |
| 2020-09-24 10:26:52     | +00:57:30     | Syncing snapshot 5112/5112     | Warping finishes |
| 2020-09-24 10:26:57     | +00:00:05     | Syncing #10300002     | Syncing new blocks started |
| 2020-09-24 20:59:4     | +09:01:27     | Syncing #10400006     | Synced 100,000 blocks | 
| 2020-09-26 20:17:52     | +47:18:09     | Syncing #10900009     | Synced 500,000 blocks |

Warping usually lasts for ~1hour on this machine . And for 100,000 it takes approximetly around ~9h. You can check full log [here](public/31rc1_mainnet_full_warp_sync.log)

Additionaly if you start syncing OE without warp and with pruning mode, the first few millions of blocks will be synced fast but that speed will gradually decrease as you climb closer to the highest block and you can expect few blocks per second when you are closer to top of the chain, this behaviour is expected.

## \-\-tracing
Trace logs are EVM output of transaction execution; they contain additional information on input/output/changes that transaction made to state. For traces to work you need to start client with “./openethereum --tracing on” For more information on what is contained in trace here is the link: [JSONRPC-trace-module](JSONRPC-trace-module)

> Note: by default tracing is turned off

