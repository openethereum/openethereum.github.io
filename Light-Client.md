---
title: Light Client
---

The first experimental light client was released with 1.7. To try out the light mode, simply run `parity --light`.

    --light                        Experimental: run in light client mode. Light clients
                                   synchronize a bare minimum of data and fetch necessary
                                   data on-demand from the network. Much lower in storage,
                                   potentially higher in bandwidth. Has no effect with
                                   subcommands (default: false).

Parity light client version >=1.11 will start the synchronization from a hardcoded block number if no related database was found on the device (e.g. the light client was launched for the first time or the database was deleted). This allows to reach the top of the chain in a matter of seconds. 

Use the flag `--no-hardcoded-sync` to prevent this behavior and sync all headers starting from the genesis block.

Read more about the `hardcodedSync` parameters in the FAQ - [How to generate a new hardcoded sync block for Parity light client](FAQ-Basic-Operations,-Configuration,-and-Synchronization.html#how-to-generate-a-new-hardcoded-sync-block-for-parity-light-client) and [chain specifications](https://wiki.parity.io/Chain-specification.html) wiki pages.

By default, each Parity node serves state data for connected light peers. To disable this, use the `--no-serve-light` command-line flag.

    --no-serve-light               Disable serving of light peers. (default: false)

Read more on the [Parity Light Protocol](The-Parity-Light-Protocol-(PIP).md) and check the [road map](https://github.com/paritytech/parity/projects/2) on GitHub.
