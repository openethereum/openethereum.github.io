As of Parity version 1.3.0 ("Acuity"), Parity supports state snapshotting. This allows for an extremely fast "synchronisation" that skips almost all of the block processing, simply injecting the appropriate data directly into the database.

**NOTE**: At present, snapshotting does not place all of the block or receipt data into the database. This means you will not get information relating to transactions more than a few days old. This is fine for some usages such as mining, but if you have or need access to historical transaction data (e.g. if you have an account that you've been using with Geth and wish to browse sent transactions) then you probably want to sync normally.

To use a snapshot sync, you first need to download a snapshot. When using Parity 1.4.0 or higher, you can just run with `parity --warp` to automatically fetch a recent snapshot from the network, restore from it, and continue syncing. On an older version, you'll have to download one via Bittorrent or other means and restore it manually.

(NOTE: These snapshots are valid for Parity 1.3.0, but not 1.4.0. As the snapshot format may be subject to change, these are not guaranteed to work with every version of Parity)

- **Standard mainnet** 
    `magnet:?xt=urn:btih:E9B318B15016AAD261E925FF19AE14B30D1BACA3&dn=mainnet-2021235.snapshot`
- **Classic** 
    `magnet:?xt=urn:btih:EAA20FC76D0C14B14FC43FC99EE3E0078B0B2161&dn=classic-2019364.snapshot`

With your snapshot downloaded, simply run Parity telling it to restore from the snapshot in question. For example, to restore the mainnet use:

`parity restore mainnet-2021235.snapshot`

To restore the Classic chain, *ensure that you use the `--chain` option* (you'll end up with a very confused database otherwise):

`parity restore classic-2019364.snapshot --chain=classic`

It takes a minute or two to restore. After that, just run Parity normally and you'll be synced in no time.
