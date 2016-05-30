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
On 1.2 and above, use Geth's export feature along with Parity's import:
```bash
mkfifo /tmp/chain.rlp && geth export /tmp/chain.rlp && sleep 1 && parity import /tmp/chain.rlp
```

Prior to 1.2, you need to run Parity and then have Geth connect to it. First run Parity without peer discovery and with a simple node key:

```bash
echo 00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 > /tmp/nodekey
parity --no-discovery --bootnodes "" --node-key $(cat /tmp/nodekey)
```

Then run Geth telling it to connect to Parity:

```bash
geth --bootnodes "enode://$(cat /tmp/nodekey)@127.0.0.1:30303" --nodiscover --maxpeers 1
```

