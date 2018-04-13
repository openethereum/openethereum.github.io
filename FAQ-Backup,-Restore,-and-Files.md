---
title: FAQ - Backup, Restore, and Files
---

### Where can I find Parity's local files?

On Windows blockchain data is located in `C:\Users\You\AppData\Local\Parity\Ethereum`
while keys and configuration are kept in `C:\Users\You\AppData\Roaming\Parity\Ethereum`

On MacOS: `/Users/you/Library/Application Support/io.parity.ethereum`

On Linux: `/home/you/.local/share/io.parity.ethereum`

### How can I backup my accounts and keys? How can I restore my keys?

See [[Backing-up-&-Restoring]].

### Can I import my keys from Geth?

They get imported automatically. :)

### Can I import accounts not created with Parity or Geth (e.g., MyEtherWallet)?

- Simply use Parity Wallet's "New Account" function on the "Accounts" page.

- Or complicated: copy it into your `$HOME/.local/share/io.parity.ethereum/keys` directory. Parity will import all keys found there.

### What are the Parity disk space needs?

This entirely depends on the selected chain and whether state trie pruning is enabled or not.

For example `--pruning archive`, which disables state trie pruning, the chain data requires around 25 GB of space for the public foundation chain. The default `--pruning fast` reduces the blockchain size to around 7 GB.

### How do I backup my blockchain?

You can just export it to a file:

```bash
parity export blocks $HOME/ethereum-chain-backup.rlp
```

### How do I restore my blockchain from a previous backup?

Just use `import`:
```bash
parity import $HOME/ethereum-chain-backup.rlp
```

### Can I import the chain from Geth?

Yes, see [Importing a Chain from Geth](Importing-a-Chain-from-Geth.md).

### How to delete the blockchain to initiate full re-sync without deleting wallet info?

- Stop parity, then execute `parity db kill`.
- Relevant options are `--chain=*` `--pruning=*` and `--db-path/--base-path`, i.e. to delete `morden` run:

```bash
parity db kill --chain=morden
```
