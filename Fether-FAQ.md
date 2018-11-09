---
title: Fether - Frequently Asked Questions
libs:
  faq: true
---

### Why is the sync so slow?

Parity Fether accesses the blockchain in a decentralised manner by using Parity Ethereum light client.
While we expect to be able to sync with the top of the blockchain in a matter of seconds in the near future, some underlying optimisations need to be made.
In the meantime, restarting Fether can help to connect to new peers and get in sync quicker.


### Why does the sync starts back to 0% when I restart Fether?

The background process to synchronize the blockchain does not restart from scratch. The sync continues from the point was left the last time Fether was launched.

### How to delete an account?

We voluntarily did not allow to delete accounts from Fether user interface. In case you really what you are doing and do not want an account not to appear on Fether, you can local and move the keystore file from the keys folder. You will find it here:

- Mac OS X: `~/Library/Application\ Support/io.parity.ethereum/keys/ethereum/`
- Linux: `$HOME/.local/share/io.parity.ethereum/keys`
- Windows 7/10: `%HOMEPATH%/AppData/Roaming/Parity/Ethereum/keys`
