---
title: Private Development Chain
---

OpenEthereum supports a `dev` mode that is particularly useful for Dapp development and demos. Thanks to its `Instant seal` consensus engine, transactions are "mined" instantly. No need to wait for the next block.  

Run a development chain using:
```
openethereum --config dev
```

The created chain is initialized with an address that contains a lot of Ether - `0x00a329c0648769a73afac7f9381e08fb43dbea72`. Its private key is `0x4d5db4107d237df6a3d58ee5f70ae63d73d7658d4026f2eefd2f204c81682cb7`.

## Customizing the development chain

The default configuration should work fine in most cases. However, it can be customised. You can see the [chain specification for the dev network](https://github.com/openethereum/openethereum/blob/master/ethcore/res/instant_seal.json) in the source repository. You can make a copy locally, modify it and pass it to the `--chain` option. The last section of the file under `accounts` can be modified to add more accounts with lots of Ether. See the [Chain specification](Chain-specification) documentation for more details.

To make multiple transactions confirm at the same time use `--reseal-min-period 0` and to make transactions free use `--gasprice 0`.
