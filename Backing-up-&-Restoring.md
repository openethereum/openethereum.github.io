It's generally sensible to back up your keys. For Parity, this is simply a case of copying a particular directory. Here's how you can do it...

on Mac:
```bash
cd $HOME/Library/Application Support/io.parity.ethereum/keys && tar czf $HOME/Desktop/parity-keys-backup.tgz * && cd -
```

on Linux:
```bash
cd $HOME/.local/share/io.parity.ethereum/keys && tar czf $HOME/Desktop/parity-keys-backup.tgz * && cd -
```

You will get a file `parity-keys-backup.tgz` in your `Desktop` path (which should therefore show up on your desktop). You can move it wherever you want - a USB pen drive might be sensible.

On Windows, the key folder can be found at `%HOMEPATH%/AppData/Roaming/Parity/Ethereum/keys`.

To restore from a previous backup:

```bash
cd $HOME/.parity/keys && tar xzf $HOME/Desktop/parity-keys-backup.tgz && cd -
```

Simple.