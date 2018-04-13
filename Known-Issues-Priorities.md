---
title: Parity Known Issues and Priorities Overview
---

This could become some kind of a rough roadmap document if well discussed and continuously maintained. If not stated otherwise, everything is sorted by priority in this document (descending). 

- If this list is ever exhausted, there is also the automatically generated priority list here: [5chdn.co/parity](https://5chdn.co/parity/priority.html). 
- The 1.10 release tracker issue can be found here: [#7699](https://github.com/paritytech/parity/issues/7699).

## Painful Bugs and Real Annoyances

Trying to prioritize stuff here. While bridging the gap to a stable light client, we should definitely look into _low-hanging_ fruits to optimize snapshot- and even full-synchronization.

### 1. Warp Sync / Snapshotting

- [ ] 1. [#6350](https://github.com/paritytech/parity/issues/6350) A warp-restore should not reset the database. Keep blocks that are in DB already. Re-downloading them is a huge, unnecessary overhead for nodes and bandwidth. After warp sync, the ancient block download should check the DB for existing blocks, not only _ancient_ blocks. 
- [ ] 2. [#7862](https://github.com/paritytech/parity/issues/7862) In addition to blockchain synchronization, the Parity client should also synchronize snapshots. Clients that are still able to generate snapshots should tell other nodes about their snapshot (height, size, etc.) and encourage them to store a copy of the snapshot along with the blockchain. This will help with snapshot distribution in the network.
- [ ] 3. [#7863](https://github.com/paritytech/parity/issues/7863) Fast-pruned nodes should be able to generate snapshots before the history required for the snapshot is pruned away. One idea would be to disable the pruning during snapshotting and continue pruning once the snapshot is available.
- [ ] 4. [#6415](https://github.com/paritytech/parity/issues/6415) Try not to abort a snapshot sync if the client restarts. After a restart, try to see if a warp was in progress and if the partial snapshot is reusable and if there are still clients available serving this snapshot.
- [ ] 5. [#7436](https://github.com/paritytech/parity/issues/7436) Look into why snapshotting fails on nodes with low memory. We won't be able to produce snapshots with OverlayRecent pruning; we should look into enabling a different pruning algorithm that does not require keeping recent states in memory.
- [ ] 6. [#7177](https://github.com/paritytech/parity/issues/7177) Figure out why fast sync sometimes just stops in the middle. This is probably obsolete if we address the first 3 points in this list.
- [ ] 7. [#7008](https://github.com/paritytech/parity/issues/7008) Figure out why ancient-blocks download sometimes stalls. Ancient blocks are important to serve the network as a full node.
- [ ] 8. [#6956](https://github.com/paritytech/parity/issues/6956) Provide RPC health status indicator for ancient blocks in addition to sync status.

### 2. Full Sync / Full Node Performance

- [ ] 0. [#7967](https://github.com/paritytech/parity/issues/7967) Significantly reduce Archive Node size.
- [ ] 1. [#7272](https://github.com/paritytech/parity/issues/7272) Auto-update should be randomly delayed. This is a critical issue because this could cause the whole network (esp. Kovan) to go down once the update was triggered.
- [ ] 2. [#7865](https://github.com/paritytech/parity/issues/7865) Let's get ParityDB integration done.
- [ ] 3. [#6593](https://github.com/paritytech/parity/issues/6593) Synchronization should never get stuck.
- [ ] 4. [#7114](https://github.com/paritytech/parity/issues/7114) Figure out why sync sometimes stalls.
- [ ] 5. [#3081](https://github.com/paritytech/parity/issues/3081) Fix this archive-node memory leak.

### 3. Light Sync / Light Client

- [ ] 1. [#6319](https://github.com/paritytech/parity/issues/6319) It's currently close to impossible to synchronize a light client. We need to figure out why this happens and fix this.
- [ ] 2. [#5642](https://github.com/paritytech/parity/issues/5642) Allow light-sync for Kovan/PoA networks.
- [ ] 3. [#6202](https://github.com/paritytech/parity/issues/6202) Enable RPCs for light clients similar to those available in a full node. This will be the ground-work for the light wallet (new UI).
- [ ] 4. [#6010](https://github.com/paritytech/parity/issues/6010) Allow warp-syncing of light clients.
- [ ] 5. [#7263](https://github.com/paritytech/parity/issues/7263) Allow syncing the light client from a trusted block hash.
- [ ] 6. [#6402](https://github.com/paritytech/parity/issues/6402) Light sync issue with stage-3 block verification failed (InvalidDifficulty).
- [ ] 7. [#7116](https://github.com/paritytech/parity/issues/7116) Consider allowing to fetch full-blocks on demand.
- [ ] 8. [#6155](https://github.com/paritytech/parity/issues/6155) Differentiate between manual throw and natural gas exhaustion.
- [ ] 9. Consider serving LES peers. Also, discuss if LES light-serving peers (Geth) provide any useful data for PIP light clients (Parity). In general, we should discuss and use similarities between LES and PIP to strengthen the light network.
- See also [Light Client Board](https://github.com/paritytech/parity/projects/2).

## Long Overdue Features

- [ ] 1. State Change Object support as discussed in [EIP #867](https://github.com/ethereum/EIPs/pull/867).
- [ ] 2. [#7162](https://github.com/paritytech/parity/issues/7162) Casper FFG proof-of-stake engine and testnet support.
- [ ] 3. A basic light wallet for users to send and receive tokens.
- [ ] 4. [#4228](https://github.com/paritytech/parity/issues/4228) _A modern Parity._ :)
