When coming from Geth, be aware that the `--geth` option provides you with the highest degree of compatibility to Geth's behaviour, even at the cost of speed, security, functionality and standardisation.

### Parity version 1.3 and above (SnapSync)

If you don't care about historical transaction information, just use our [state-snapshotting](Getting Synced) to get synced really fast.

### Parity version 1.2 and above

On 1.2 and above, use Geth's export feature along with Parity's import:
```bash
mkfifo /tmp/chain.rlp && geth export /tmp/chain.rlp && sleep 1 && parity import /tmp/chain.rlp
```

### Parity versions 1.0 and 1.1

Prior to 1.2, you need to run Parity and then have Geth connect to it. First run Parity without peer discovery and with a simple node key:

```bash
echo 00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 > /tmp/nodekey
parity --no-discovery --bootnodes "" --node-key $(cat /tmp/nodekey)
```

Then run Geth telling it to connect to Parity:

```bash
geth --bootnodes "enode://$(cat /tmp/nodekey)@127.0.0.1:30303" --nodiscover --maxpeers 1
```
