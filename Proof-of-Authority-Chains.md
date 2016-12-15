**Currently available only in the `master` branch, please compile or or use a recent [nightly build](https://gitlab.ethcore.io/Mirrors/ethcore-parity/builds).**

Parity supports a Proof-of-Authority consensus engine to be used with EVM based chains. Proof-of-Authority is a replacement for Proof-of-Work, which can be used for private chain setups.

It does not depend on nodes solving arbitrarily difficult mathematical problems, but instead uses
a hard-configured set of "authorities" - nodes that are explicitly allowed to create new blocks and secure the blockchain. This makes it easier to maintain a private chain and keep the block issuers accountable.

[![Gitter](https://badges.gitter.im/ethcore/parity-poa.svg)](https://gitter.im/ethcore/parity-poa?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

# Setting up a Proof of Authority network
1. Pick real world entities that should control the network, so called authorities.
2. Each authority should run a Parity node and create an account.
3. Authorities should exchange their account addresses and put all of them in the JSON chain specification file.
4. All authority nodes should run an instance Parity with the agreed on specification.
5. Additional nodes can connect to the network as well to use it.
6. Use the network just as a public Ethereum network (transactions, contracts etc.).

# Running the authority nodes

Each node on the network should run its node with chain spec JSON file provided to `--chain` option. Format of such file depends on the consensus `Engine` used and is described in the next section.

If you're expecting to issue blocks, make sure you have `--engine-signer` set to one of the `authorities` and password file is provided to `--password`. You should ensure anyone else you want issuing on the network is similarly configured. Each authority can only run a **single node** and to utilize the full network capacity each authority should run a node.
The configuration can also be done via the [config file](https://ethcore.github.io/parity-config-generator/) with the following fields:

```
[parity]
chain = "/path/to/json/spec"

[account]
password = "/path/to/password"

[mining]
engine_signer = ["0x37f93cfe411fa244b87ff257085ee360fca245e8"]
reseal_on_txs = "none"
```

Authorities reseal automatically, no need to reseal on transactions: `--reseal-on-txs none`.

## Private centralized network setup
When deploying PoC it is useful to get a centrally managed network going first. With Parity the nodes can be identical and simply expose the RPC interface to a central managing node, all further setup can be done via those interfaces.

### Useful RPCs
Connect the deployed nodes:
- `parity_enode()` returns enode of the particular instance
- `parity_addReservedPeer(enode)` adds enode to the list of reserved peers (run with `--reserved-only` to avoid other connections)

Create accounts:
- `personal_newAccount(password)` creates new account and returns its address
- `parity_newAccountFromPhrase(phrase, password)` creates an account deterministically and returns its address (always the same for a given input)

Validate the blocks:
- `parity_setEngineSigner(address, password)` set this to one of authority addresses in order to participate in the consensus

Further methods for actually using the nodes can be found [here](https://github.com/ethcore/parity/wiki/JSONRPC-eth-module).

# Chain Engine specification

To create a Proof of Authority chain a JSON spec file should be provided to `--chain` with authorities set to correct addresses.

An example template that can be used for the consensus algorithms below:
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
		"0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
		"0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
		"0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
		"0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
		"9cce34f7ab185c7aba1b7c8140d620b4bda941d6": { "balance": "1606938044258990275541962092341162602522202993782792835301376" }
	}
}
```

## Authority Round Engine

Engine should be specified as follows:
```
"engine": {
	"AuthorityRound": {
		"params": {
			"gasLimitBoundDivisor": "0x400",
			"stepDuration": "5",
			"authorities" : [
				"0x37f93cfe411fa244b87ff257085ee360fca245e8",
				"0x610a3a37b98bf0c91c35442e489c246096739324"
			]
		}
	}
}
```
and genesis seal (this should be the same for all chains):
```
"seal": {
	"authority_round": {
		"step": "0x0",
		"signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
	}
}
```

The `authorities` are the addresses that are able to issue blocks. `stepDuration` is the minimum block time in seconds.

If malicious authorities are possible then `--force-sealing` is advised, this will ensure that the correct chain is the longest (making it BFT with finality of `authorities_count * step_duration` given no network partitions).

## Tendermint Engine

This consensus is still experimental, please use Authority Round for more stable solution.

```
"engine": {
	"AuthorityRound": {
		"params": {
			"gasLimitBoundDivisor": "0x400",
			"authorities" : [
				"0x37f93cfe411fa244b87ff257085ee360fca245e8",
				"0x610a3a37b98bf0c91c35442e489c246096739324"
			]
		}
	}
}
```
and genesis seal (this should be the same for all chains):
```
"seal": {
	"tendermint": {
		"round": "0x0",
		"proposal": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
		"precommits": [
			"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
		]
	}
}
```