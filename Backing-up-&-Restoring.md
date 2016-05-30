It's generally sensible to back up your keys. For Parity, this is simply a case of copying a particular directory. Here's how:

```bash
cd $HOME/.parity/keys && tar czf $HOME/Desktop/parity-keys-backup.tgz * && cd -
```

You will get a file `parity-keys-backup.tgz` in your `Desktop` path (which should therefore show up on your desktop). You can move it wherever you want - a USB pen drive might be sensible.

To restore from a previous backup:

```bash
cd $HOME/.parity/keys && tar xzf $HOME/Desktop/parity-keys-backup.tgz && cd -
```

Simple.