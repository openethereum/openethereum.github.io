###### How do I build and install?
Once you have the source directory and Rust installed, just use cargo and copy the resulting binary:
```bash
cargo build --release && cp target/release/parity /usr/local/bin
```

###### Why doesn't `--geth` option work?
It's only available on version 1.2 and above. Check `parity --version` to make sure you're not on an older version.

###### How do I backup my blockchain?
You can just export it to a file:
```bash
parity export $HOME/ethereum-chain-backup.rlp
```

###### How do I restore my blockchain from a previous backup?
Just use `import`:
```bash
parity import $HOME/ethereum-chain-backup.rlp
```

###### How do I backup/restore my keys?
See [[Backing-up-&-Restoring]].

###### How do I import my chain from Geth?
See [[Importing a Chain from Geth]].

###### When I start parity with the `homestead_test.json` I get a 0 block chain, but loading `ethminer` never mines a block.

`homestead_test.json` is a file meant for testing on a chain very similar to the Homestead chain. As such it is of equivalent (substantial) difficulty to mine on it. Rather what you need is something of minimal mining difficulty, like Morden. See more instructions [here](https://github.com/ethcore/parity/wiki/Private-chains).