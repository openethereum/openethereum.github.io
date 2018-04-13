---
title: Development Overview
---

### Smart Contracts

Parity Ethereum supports state-orientated programming of [Smart Contracts](Smart-Contract.mds) with the Solidity programming language for the Ethereum blockchain. The wallet contains an integrated Solidity development environment allowing you to write, compile, and deploy contracts.

### ERC-20 Tokens

The native Ethereum token is called Ether (ETH). In addition to Ether, additional assets and [Tokens](Tokens.md) can be handled by the Ethereum blockchain. The Parity Ethereum wallet supports any ERC20-compliant token by displaying balances and enabling transfers to be as simple as Ether transactions. Read more on [Token Deployment](Token-Deployment.md) and the Parity [Token Registry](Token-Registry.md).

### Decentralized Applications

There is a 10-part [Tutorial](Dapp-Tutorial.md) on how to write dapps. It makes significant use of React and Bond-based UI technologies together with the Parity counterparts. By the end of it, you'll have some familiarity with all aspects of dapp-authoring including contract deployment, transaction posting, state-inspection and event-tracking. Examples of common operations and useful functionality when building a dApp using [oo7-Bonds](oo7-Parity-Reference.md) can be [found here](oo7-Parity-Examples.md). See also [Deploying Dapps to Parity Wallet](Deploying-DApps-to-Parity-Wallet.md), [Dapp Discovery](Register-your-DAPP-for-discovery.md) and the [Parity Dapp Registry](Parity-dapp-registry.md).

### Development Chains

By default, when simply running `parity`, Parity Ethereum will connect to the official public Ethereum network. In order to run a [chain different to the official public Ethereum one](Chain-specification.md), Parity has to be ran with the `--chain` option or with a [config file](Configuring-Parity#config-file.md) specifying `chain = "path"` under `[parity]`. There are a few named presets that can be selected from or a custom JSON spec file can be supplied.

Parity supports [private chain](Private-development-chain.md) and private network configuration via [Chain specification](Chain-specification.md) files provided with `--chain`. In addition to the usual [Proof of Work Chains](Proof of Work Chains.md), Parity also includes [Proof of Authority Chains](Proof-of-Authority-Chains.md) which do not require mining. More details on the available options can be found on the [Pluggable Consensus](Pluggable-Consensus.md) page. See also the [Demo PoA Tutorial](Demo-PoA-tutorial.md).

### Application Programming Interface

Last but not least, have a look at the [JSONRPC Guide](JSONRPC.md).
