---
title: Private Development Chain
---

OpenEthereum supports a `dev` mode that is particularly useful for Dapp development and demos. Thanks to its `Instant seal` consensus engine, transactions are "mined" instantly. No need to wait for the next block.  

Run a development chain using:
```
openethereum --config dev
```

## Customizing the development chain

The default configuration should work fine in most cases. However, it can be customised. You can see the [chain specification for the dev network](https://github.com/openethereum/openethereum/blob/master/ethcore/res/instant_seal.json) in the source repository. You can make a copy locally, modify it and pass it to the `--chain` option. The last section of the file under `accounts` can be modified to add more accounts with lots of Ether. See the [Chain specification](Chain-specification) documentation for more details.

To make multiple transactions confirm at the same time use `--reseal-min-period 0` and to make transactions free use `--gasprice 0`.
