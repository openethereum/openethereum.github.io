---
title: Proof-of-Authority Chains
---

Parity supports a Proof-of-Authority consensus engine to be used with EVM based chains. Proof-of-Authority is a replacement for Proof-of-Work, which can be used for private chain setups.

It does not depend on nodes solving arbitrarily difficult mathematical problems, but instead uses
a set of "authorities" - nodes that are explicitly allowed to create new blocks and secure the blockchain. The chain has to be signed off by the majority of authorities, in which case it becomes a part of the permanent record. This makes it easier to maintain a private chain and keep the block issuers accountable.

For consortium setting there are no disadvantages of PoA network as compared to PoW. It is more secure (since an attacker with unwanted connection or hacked authority can not overwhelm a network potentially reverting all transactions), less computationally intensive (mining with difficulty which provides security requires lots of computation), more performant (Aura consensus provides lower transaction acceptance latency) and more predictable (blocks are issued at steady time intervals). PoA deployments are used by the enterprise and by the public (e.g. popular Kovan test network).

[![Gitter](https://badges.gitter.im/ethcore/parity-poa.svg)](https://gitter.im/ethcore/parity-poa?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

## Setting up a Proof of Authority network
1. Pick real world entities that should control the network, so called authorities.
2. Each authority should run a Parity node and create an account.
3. Authorities should exchange their account addresses and place them in the [spec or validator contract](Pluggable-Consensus.md#validator-engines).
4. All authority nodes should run an instance Parity with the agreed on specification.
5. Additional user nodes can connect to the network as well.
6. Use the network just as a public Ethereum network (transactions, contracts etc.).

A [Demo PoA tutorial](Demo-PoA-tutorial.md) describes how to set up two nodes and connect them to each other.

## Running the authority nodes

Each node on the network should run its node with chain spec JSON file provided to `--chain` option. Format of such file depends on the consensus `Engine` used and is described on the [Chain specification](Chain-specification.md) page.

If you're expecting to issue blocks, make sure you have `--engine-signer` set to an account address (`0xADDRESS`) listed in the engine configuration under `authorities` and password file for that account is provided to `--password`. Parity has a separate directory for each chain, so make sure that the account is visible on the specified chain (create an account with `--chain`, import the keys or use `--keys-path`). You should ensure anyone else you want issuing on the network is similarly configured. Each authority can only run a **single node** and to utilize the full network capacity each authority should run a node.
To make the transactions free, authority nodes can run with `--usd-per-tx 0`.
The configuration can also be done via the [config file](Configuring-Parity.md#config-file) with the following fields:

```
[parity]
chain = "/path/to/json/spec"

[account]
password = ["/path/to/password"]

[mining]
engine_signer = "0x37f93cfe411fa244b87ff257085ee360fca245e8"
reseal_on_txs = "none" // Authorities reseal automatically
usd_per_tx = "0" // Allows for free transactions.
```

### Private centralized network setup
When deploying PoC it is useful to get a centrally managed network going first. With Parity the nodes can be identical and simply expose the RPC interface to a central managing node, all further setup can be done via those interfaces.

#### Community built setups
- [Docker compose network with 3 authorities and netstats dashboard](https://github.com/dstarcev/parity-poa-playground)
- [Docker compose for running integration tests](https://github.com/illya13/parity-poa)
- [Docker compose with demo PoA setup](https://github.com/vincentserpoul/parityPoA)

#### Useful RPCs
Connect the deployed nodes:
- `parity_enode()` returns enode of the particular instance
- `parity_addReservedPeer(enode)` adds enode to the list of reserved peers (run with `--reserved-only` to avoid other connections)

Create accounts:
- `personal_newAccount(password)` creates new account and returns its address
- `parity_newAccountFromPhrase(phrase, password)` creates an account deterministically and returns its address (always the same for a given input)

Validate the blocks:
- `parity_setEngineSigner(address, password)` set this to one of authority addresses in order to participate in the consensus

Further methods for actually using the nodes can be found [here](JSONRPC-eth-module.md).

## Chain Engine specification

To create a Proof of Authority chain a JSON spec file should be provided to `--chain` with authorities set to correct addresses.

An example template that can be used for the consensus algorithms:
```
{
	"name": "ChainName",
	"engine": ENGINE_HERE,
	"params": {
		"accountStartNonce": "0x0",
		"maximumExtraDataSize": "0x20",
		"minGasLimit": "0x1388",
		"networkID" : "0x2323"
	},
	"genesis": {
		"seal": SEAL_HERE,
		"difficulty": "0x20000",
		"author": "0x0000000000000000000000000000000000000000",
		"timestamp": "0x00",
		"parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"extraData": "0x",
		"gasLimit": "0x5B8D80"
	},
	"accounts": {
		"0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
		"0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
		"0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
		"0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
		"0x9cce34f7ab185c7aba1b7c8140d620b4bda941d6": { "balance": "1606938044258990275541962092341162602522202993782792835301376" }
	}
}
```

The two available algorithms are the [Aura](Pluggable-Consensus.md#aura) and [Tendermint](Pluggable-Consensus.md#tendermint). More details on other chain parameters are available on the [Chain specification](Chain-specification.md) page.
