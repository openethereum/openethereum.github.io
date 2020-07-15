---
title: Development Overview
---

By default, when simply running `openethereum`, OpenEthereum will connect to the Ethereum Mainnet. In order to run a [different chain](Chain-specification), OpenEthereum has to be run with the `--chain` option or with a [config file](Configuring-OpenEthereum#config-file) specifying `chain = "path"` under `[parity]`. There are a few named presets that can be selected from or a custom JSON spec file can be supplied.

OpenEthereum supports [private chain](Private-development-chain) and private network configuration via [Chain specification](Chain-specification) files provided with `--chain`. In addition to the usual [Proof of Work Chains](Proof-of-Work-Chains), OpenEthereum also includes [Proof of Authority Chains](Proof-of-Authority-Chains) which do not require mining. More details on the available options can be found on the [Pluggable Consensus](Pluggable-Consensus) page. See also the [Demo PoA Tutorial](Demo-PoA-tutorial).

Last but not least, have a look at the [JSONRPC Guide](JSONRPC).
