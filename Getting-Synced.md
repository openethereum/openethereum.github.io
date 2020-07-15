---
title: Getting Synced
---

OpenEthereum supports state snapshotting that significantly reduces sync-times and database pruning which reduces disk requirements. Both features are enabled by default. Simply get synced by running:

```bash
$ openethereum
```

## Warp Synchronization

State snapshotting, or Warp Sync, allows for a very fast "synchronization" that skips almost all of the block processing, simply injecting the appropriate data directly into the database. To use a snapshot sync, you first need to download a snapshot.

Note, at present, snapshotting does not place all of the block or receipt data into the database. This means you will not get information relating to transactions more than a few days old until after initially syncing to the chain head. This is fine for some usages such as mining, but if you have or need access to historical transaction data (e.g. if you have an account that you've been using with Geth and wish to browse sent transactions) then you probably want to sync normally.

To disable snapshotting, run OpenEthereum with:

```bash
$ openethereum --no-warp
```

CLI reference:

```bash
  --no-warp                      Disable syncing from the snapshot over the network. (default: false)
```

To verify the status of warp sync, you can use the [`parity_chainStatus`](JSONRPC-parity-module#parity_chainstatus) RPC method.

### Database Pruning

OpenEthereum's database pruning mode is enabled by default and only maintains a small journal overlay reducing the disk space required by your OpenEthereum node significantly.

To disable storage pruning and to keep all state-trie data, run OpenEthereum with:

```bash
$ openethereum --pruning archive
```

Further fine-tuning of the pruning is enable via the `--pruning history` and `--pruning-memory` flags. Full CLI reference:

```bash
  --pruning METHOD               Configure pruning of the state/storage trie. METHOD
                                 may be one of auto, archive, fast:
                                 archive - keep all state trie data. No pruning.
                                 fast - maintain journal overlay. Fast but 50MB used.
                                 auto - use the method most recently synced or
                                 default to fast if none synced (default: auto).
  --pruning-history NUM          Set a minimum number of recent states to keep when pruning
                                 is active. (default: 64).
  --pruning-memory MB            The ideal amount of memory in megabytes to use to store
                                 recent states. As many states as possible will be kept
                                 within this limit, and at least --pruning-history states
                                 will always be kept. (default: 75)
```
