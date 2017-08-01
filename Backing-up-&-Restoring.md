It's generally sensible to back up your keys and recovery phrases.

### Role of Parity recovery phrases

The Parity recovery phrase is a 12-word mnemonic phrase allowing you to have an additional backup. It's displayed once after account creation and should be written down or printed out and kept in a safe place. This phrase is not protected with any password or encryption and should always kept offline and _cold_. In the rare case that your encrypted backups (see below) are corrupted or lost, the phrase allows you to fully recover your private key.

### Exporting accounts via the Wallet UI

##### A single account

![accounts-export-0](images/accounts-export-0.png)

![accounts-export-1](images/accounts-export-1.png)

##### All accounts at once

![accounts-export-2](images/accounts-export-2.png)

![accounts-export-3](images/accounts-export-3.png)

![accounts-export-4](images/accounts-export-4.png)

### Exporting accounts via CLI

##### Available RPC APIs

parity_exportAccount

curl --data '{"method":"parity_exportAccount","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","hunter2"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545

##### Manual archiving via CLI

### Backing up wallets and vaults

### Restoring options






 For Parity, this is simply a case of copying a particular directory. Here's how you can do it...

on Mac:
```bash
cd $HOME/Library/Application\ Support/io.parity.ethereum/keys && tar czf $HOME/Desktop/parity-keys-backup.tgz * && cd - && cd -
```

on Linux:
```bash
cd $HOME/.local/share/io.parity.ethereum/keys && tar czf $HOME/Desktop/parity-keys-backup.tgz * && cd -
```

On Windows, the key folder can be found at `%HOMEPATH%/AppData/Roaming/Parity/Ethereum/keys`.

You will get a file `parity-keys-backup.tgz` in your `Desktop` path (which should therefore show up on your desktop). You can move it wherever you want - a USB pen drive might be sensible.



To restore from a previous backup...

on Mac:
```bash
cd $HOME/Library/Application Support/io.parity.ethereum/keys && tar xzf $HOME/Desktop/parity-keys-backup.tgz * && cd -
```

on Linux:
```bash
cd $HOME/.local/share/io.parity.ethereum/keys && tar xzf $HOME/Desktop/parity-keys-backup.tgz * && cd -
```

Simple.
