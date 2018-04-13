---
title: IPFS
---

Parity is listening for Ethereum-IPFS compatible calls. Currently supported thus far: `eth-block`, `eth-block-list`, `eth-tx`, and `eth-state-trie`. Any other IPFS API calls will not return anything.

### Implementation Status

`js-ipfs` has support for Ethereum objects: IPFS can access the Ethereum blockchain on demand without having to constantly transfer the blocks. For that, the EthereumJS team was building a storage backend for `js-ipfs` that uses Parity, so that you can _read_ blocks from the Ethereum chain. Track upstream progress at: [ipfs/js-ipfs/#763](https://github.com/ipfs/js-ipfs/issues/763)

Parity implementation status overview:

| Available | Type                 |      Name                              |  Code |
|-----------|----------------------|----------------------------------------|-------|
| `[x]`     |`eth-block`           | Ethereum Block (block header)          | `0x90`|
| `[x]`     |`eth-block-list`      | Ethereum Block List (block ommers)     | `0x91`|
| `[ ]`     |`eth-tx-trie`         | Ethereum Transaction Trie Node         | `0x92`|
| `[x]`     |`eth-tx`              | Ethereum Transaction                   | `0x93`|
| `[ ]`     |`eth-tx-receipt-trie` | Ethereum Transaction Receipt Trie Node | `0x94`|
| `[ ]`     |`eth-tx-receipt`      | Ethereum Transaction Receipt           | `0x95`|
| `[x]`     |`eth-state-trie`      | Ethereum State Trie Node               | `0x96`|
| `[ ]`     |`eth-storage-trie`    | Ethereum Contract Storage Trie Node    | `0x98`|

Please subscribe to [#4172](https://github.com/paritytech/parity/issues/4172) for more details on Parity's implementation progress.

### Example Requests

The `eth-ipfs-bridge` project allows early testing of the IPFS-implementation in Parity: [kumavis/ipfs-eth-bridge](https://github.com/kumavis/ipfs-eth-bridge)

This allows anyone on the network to access Ethereum objects by their canonical hash as encoded by CIDs:

```
http://localhost:5001/api/v0/block/get?arg=z43AaGF5tmkT9SEX6urrhwpEW5ZSaACY73Vw357ZXTsur2fR8BM
```

See also the `explore-ethereum` examples provided by `js-ipfs`: [ipfs/js-ipfs/examples/explore-ethereum](https://github.com/ipfs/js-ipfs/tree/master/examples/explore-ethereum)

### CLI reference:

The IPFS HTTP API is disabled by default. Enable it with `--ipfs-api` to expose it to `localhost:5001`.

```bash
    --ipfs-api                     Enable IPFS-compatible HTTP API. (default: false)
    --ipfs-api-port PORT           Configure on which port the IPFS HTTP API should listen.
                                   (default: 5001)
    --ipfs-api-interface IP        Specify the hostname portion of the IPFS API server,
                                   IP should be an interface's IP address or local.
                                   (default: local)
    --ipfs-api-cors URL            Specify CORS header for IPFS API responses.
                                   (default: None)
    --ipfs-api-hosts HOSTS         List of allowed Host header values. This option will
                                   validate the Host header sent by the browser, it
                                   is additional security against some attack
                                   vectors. Special options: "all", "none"
                                   (default: none).
```
