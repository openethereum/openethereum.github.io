---
title: FAQ - CLI, Mining, and Networks
libs:
  faq: true
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

## What does Parity's command line output mean?

When running parity using the command line, a lot of information is displayed in the terminal. Here is a description of these logs.
### While syncing the full node
Example: `2018-04-30 16:10:13 Syncing #5532478 9ce2…0499 2 blk/s 391 tx/s 15 Mgas/s 0+ 17 Qed #5532493 25/25 peers 5 MiB chain 128 MiB db 2 MiB queue 3 MiB sync RPC: 0 conn, 0 req/s, 0 µs`
- `2018-04-30 16:10:13`: timestamp of the log
- `Syncing #5532478`: block number currently processed
- `9ce2…0499`: block's hash
- `2 blk/s`: average block(s) processed per second
- `391 tx/s`: average transactions processed per second
- `15 Mgas/s`: million gas processed per second
- `0+`: unverified block queue size
- `17 Qed`: block queue size
- `#5532493`: highest block number seen on the network
- `25/25 peers`: number of active peers  / configured maximum number of peers
- `5 MiB db`: state database memory used
- `128 MiB chain`: blockchain cache info memory used
- `5 MiB queue`: queue memory used (contains information about the queued blocks)
- `3 MiB sync`: sync memory used (contains information about the connected peers, last imported block, etc.)
- `RPC 0 conn`: number of RPC connections to the node
- `0 req/s`: number of RPC request per seconds 
- `0 µs`: average RPC roundtrip time for a single request

### Once the node is synced with the top of the chain
Example: `2018-05-02 12:33:23  Imported #3153278 28e4…9981 (12 txs, 3.19 Mgas, 6.84 ms, 12.40 KiB) + another 1 block(s) containing 5 tx(s)`
- `2018-05-02 12:33:23`: timestamp of the log
- `Imported #3153278`: block number received from peers
- `28e4…9981`: block's truncated hash
- `12 txs`: number of transactions in the block
- `3.19 Mgas`: million gas used in the imported block
- `6.84 ms`: time it took to process the block
- `12.40 KiB`: block size
- `+ another 1 block(s) containing 5 tx(s)`: appears when 2 or more blocks are imported within 1 tick of informant (one line in the console) - In that particular example it means that 2 blocks were imported since last `Imported` line and the second had 5 transactions.

### While syncing in light mode
Example: `2018-05-02 14:51:02  Syncing   #14677 aa97…42d2  1568 hdr/s      0+20139 Qed    #14677    9/50 peers   9 MiB cache 18 MiB queue  RPC:  0 conn,  0 req/s,   0 µs`
- `1568 hdr/s` average number of processed headers per second
- `aa97…42d2`: header's truncated hash
- `0+`: unverified header queue size
- `20139 Qed`: header queue size
- `#14677`: highest block number seen on the network
- `9/50 peers`: number of active peers  / configured maximum number of peers
- `5 MiB db`: state database memory used
- `9 MiB cache`: blockchain cache info memory used
- `18 MiB queue`: queue memory used (contains information about the queued headers)
- `RPC 0 conn`: number of RPC connections to the node
- `0 req/s`: number of RPC request per seconds 
- `0 µs`: average RPC roundtrip time for a single request

### Once the node is synced with the top of the chain in light mode
Example: `2018-05-02 15:20:41  Imported #5543645 0x86ed…6288 (7.99 Mgas) + another 3 header(s)`
- `2018-05-02 15:20:41`: timestamp of the log
- `Imported #5543645`: header block number received from peers
- `0x86ed…6288`: header's truncated hash
- `7.99 Mgas`: million gas used in the imported block
- `+ another 3 header(s)`: appears when 2 or more headers are imported within 1 tick of informant (one line in the console) - In that particular example it means that 4 headers were imported since last `Imported` line

All other command line columns are described in full node logs above.

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
