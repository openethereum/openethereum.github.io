---
title: Getting Synced
---

Parity supports state snapshotting that significantly reduces sync-times and database pruning which reduces disk requirements. Both features are enabled by default on most recent Parity releases. Simply get synced by running:

```bash
$ parity
```

## Warp Synchronization

State snapshotting, or warp-sync, allows for an extremely fast "synchronization" that skips almost all of the block processing, simply injecting the appropriate data directly into the database. To use a snapshot sync, you first need to download a snapshot.

When using Parity **1.4** or **1.5**, you can just run with:

```bash
$ parity --warp
```

... to automatically fetch a recent snapshot from the network, restore from it, and continue syncing. When using Parity **1.6** or **1.7**, `--warp` does nothing as it is enabled by default.

Note, at present, snapshotting does not place all of the block or receipt data into the database. This means you will not get information relating to transactions more than a few days old. This is fine for some usages such as mining, but if you have or need access to historical transaction data (e.g. if you have an account that you've been using with Geth and wish to browse sent transactions) then you probably want to sync normally.

To disable snapshotting, run Parity with:

```bash
$ parity --no-warp
```

CLI reference:

```bash
  --warp                         Does nothing; Warp sync is on by default. (default: true)
  --no-warp                      Disable syncing from the snapshot over the network. (default: false)
```

### Database Pruning

Parity's database pruning mode is enabled by default and only maintains a small journal overlay reducing the disk space required by your Parity node significantly.

To disable storage pruning and to keep all state-trie data, run Parity with:

```bash
$ parity --pruning archive
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
