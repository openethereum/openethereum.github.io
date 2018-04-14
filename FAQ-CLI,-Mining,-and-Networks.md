---
title: FAQ - CLI, Mining, and Networks
---

## How do I mine with Parity?

Parity supports standard Ethereum JSON-RPC interface for mining ([eth_getWork] (JSONRPC-eth-module.md#eth_getwork), [eth_submitWork](JSONRPC-eth-module.md#eth_submitwork) methods) and thus compatible with any miner which implements Ethereum Proof-of-Work.

First get a Parity node up and running (either build yourself or install one of the packages; the [Setup](Setup.md) guide can help you). Next, you'll need to install your preferred miner. Read more on [Mining](Mining.md).

## Which chains are supported by Parity?

Parity supports multiple public chain configurations:

    --chain CHAIN                  Specify the blockchain type. CHAIN may be either a
                                   JSON chain specification file or olympic, frontier,
                                   homestead, mainnet, morden, ropsten, classic, expanse,
                                   testnet, kovan or dev (default: homestead).

- `--chain olympic` Runs the pre-release Ethereum Olympic testnet with network ID `0`.
- `--chain frontier` Runs the first released Ethereum public network with ID `1`. It does not include the Homestead and DAO hardforks.
- `--chain homestead` Runs the latest version of the Ethereum public network with ID `1`.
- `--chain mainnet` Same as `homestead`.
- `--chain foundation` Same as `homestead`.
- `--chain morden` Runs the first Ethereum public testnet with ID `2`, still used as Ethereum Classic public testnet.
- `--chain ropsten` Runs the second Ethereum public testnet with ID `3`.
- `--chain classic` Runs the Ethereum Classic public network which opposed the DAO hardfork.
- `--chain expanse` Runs the Expanse public network with ID `1`.
- `--chain kovan` Runs the Proof-of-Authority public testnet with ID `42`.
- `--chain testnet` Same as `kovan`.
- `--chain dev` Runs a private testnet configuration, see [Private development chain](Private-development-chain.md).

See also [Chain specification](Chain-specification.md).

## What are the different consensus engines available for Parity?

- Ethash (Ethereum Proof-of-Work Engine), see [Ethash](https://github.com/ethereum/wiki/wiki/Ethash).
- Instant Seal (Development Engine), see [Private development chain](Private-development-chain.md).
- Aura (Proof-of-Authority Validator Engine), see [Aura](Aura.md).
- Tendermint (Experimental Validator Engine), see [Validator Engines](https://wiki.parity.io/Consensus-Engines#validator-engines)

## How do I get Ether for the Morden, Ropsten, or Kovan testnets?

- See [Morden and Ropsten Faucets](http://ethereum.stackexchange.com/q/84).
- See [Kovan Faucets](https://github.com/kovan-testnet/faucet).

## Does Parity provide a JavaScript console?

Yes, the Parity Wallet contains a Web3 console application. Navigate to the _'Applications'_ tab and open the _'Parity/Web3 Console'_.

## None of my JSONRPC requests work, they all fail with no output!?!

As a safeguard against a certain type of attack, Parity requires you to use a special content type. Just add `-H "Content-Type: application/json"` to your `curl` command.

## When I start Parity with the 'homestead_test.json' I get a 0 blocks chain, and mining never seals a block!?!

`homestead_test.json` is a file meant for testing on a chain very similar to the Homestead chain. As such it is of equivalent (substantial) difficulty to mine on it. Rather what you need is something of minimal mining difficulty, like Morden. See more instructions [here](Private-chains.md).

## Why do my transactions not get mined?

Your gas price is probably too low. Try increasing the gas price to 20Gwei by appending `, gasPrice: 20000000000` into your transaction spec or `sendTransaction` options.

## All my 'eth_sendTransaction' calls only return '0x00000000...00000000' as return value!?!

You're probably trying to send from a locked account. Ensure you use the `--unlock` (to specify which account(s) to unlock) and `--password` (to pass in the file contain any passwords needed to unlock those acocunts) so that the account(s) from which you're sending the transaction are unlocked.

## I've received an abuse message from my ISP/hosting provider. Something about malicious activity triggered by Parity.

See [Network Configuration](https://wiki.parity.io/Network-Configuration) for some tips on how to configure Parity to behave.
