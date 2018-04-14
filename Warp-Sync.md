---
title: Warp Sync (`par`)
---

Warp Sync is an extension to the Ethereum Wire protocol, which involves sending snapshots over the network to get the full state at a given block extremely quickly, and then filling in the blocks between the genesis and the snapshot in the background.

Warp Sync is a subprotocol built on the DevP2p networking layer, with 3-byte identifier `par`.

Documentation on the snapshot and manifest format [can be found here](Warp-Sync-Snapshot-Format.md).

## How do I use it?
Easy! Just update to Parity `1.4.0` or later, and run `parity --warp`. Your client will search for peers with a warp sync snapshot and use it if one is found.

## Wire messages
The warp protocol inherits all packets from version 63 of the `eth` wire protocol, as well as amending the status packet (`0x01`) and adding a few new ones:

**Status**
[`0x00`, `...`, `snapshot_hash`: B_32, `snapshot_number`: P] In addition to all the fields in `eth` protocol version 63's status (denoted by `...`), include `snapshot_hash` and `snapshot_number` which signify the snapshot manifest RLP hash and block number respectively of the peer's local snapshot.

**GetSnapshotManifest**
[`0x11`] Request a snapshot manifest in RLP form from a peer.

**SnapshotManifest**
[`0x12`, `manifest` or nothing] Respond to a **GetSnapshotManifest** message with either an empty RLP list or a 1-item RLP list containing a snapshot manifest

**GetSnapshotData**
[`0x13`, `chunk_hash`: B_32] Request a chunk (identified by the given hash) from a peer.

**SnapshotData**
[`0x14`, `chunk_data` or nothing] Respond to a **GetSnapshotData** message with either an empty RLP list or a 1-item RLP list containing the raw chunk data requested.
