Parity supports a Proof-of-Authority consensus engine to be used with EVM based chains. Proof-of-Authority is a replacement for Proof-of-Work, which can be used for private chain setups.

It does not depend on nodes solving arbitrarily difficult mathematical problems, but instead uses
a hard-configured set of "authorities" - nodes that are explicitly allowed to create new blocks and secure the blockchain. This makes it easier to maintain a private chain and keep the block issuers accountable.

## Engine specification

To run the above, chain spec JSON file provided to `--chain` has to have an engine such as this:
```
"engine": {
	"AuthorityRound": {
		"params": {
			"gasLimitBoundDivisor": "0x400",
			"stepDuration": "2",
			"authorities" : [
				"0x37f93cfe411fa244b87ff257085ee360fca245e8",
				"0x610a3a37b98bf0c91c35442e489c246096739324"
			]
		}
	}
}
```
and genesis seal:
```
"seal": {
	"generic": {
		"fields": 2,
		"rlp": "0x200"
	}
}
```

The `authorities` are the addresses that are able to issue blocks. `stepDuration` is the minimum block time in seconds.

If you're expecting to issue blocks, make sure you have `--author` set to one of the `authorities` and that said key is `--unlock`ed (with `--password` file provided). You should ensure anyone else you want issuing on the network is similarly configured.
The configuration can also be done via the [config file](https://ethcore.github.io/parity-config-generator/) with the following fields:

```
[parity]
chain = "/path/to/json/spec"

[account]
unlock = ["0x37f93cfe411fa244b87ff257085ee360fca245e8"]
password = "/path/to/password"

[mining]
author = "0x37f93cfe411fa244b87ff257085ee360fca245e8"
reseal_on_txs = "none"
# force_sealing = true # if malicious authorities are present.
```

If malicious authorities are possible then `--force-sealing` is advised, this will ensure that the correct chain is the longest (making it BFT with finality of `authorities_count * step_duration` given no network paritions).

Authorities do not have to reseal on transactions: `--reseal-on-txs none`.

## Private centralized network setup
When deploying PoC it is useful to get a centrally managed network going first. With Parity the nodes can be identical and simply expose the RPC interface to a central managing node, all further setup can be done via those interfaces.

### Useful RPCs
Connect the deployed nodes:
- `parity_enode()` returns enode of the particular instance
- `parity_addReservedPeer(enode)` adds enode to the list of reserved peers (run with `--reserved-only` to avoid other connections)

Create accounts:
- `personal_newAccount(password)` creates new account and returns its address
- `parity_newAccountFromPhrase(password, phrase)` creates an account deterministically and returns its address (always the same for a given input)

Validate the blocks:
- `parity_setAuthor(address)` set this to one of authority addresses
- `personal_unlockAccount(address, password, 0)` unlock that account permanently to issue blocks

Further methods for actually using the nodes can be found [here](https://github.com/ethcore/parity/wiki/JSONRPC-eth-module).


