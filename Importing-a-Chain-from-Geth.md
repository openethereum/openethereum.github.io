---
title: Importing a Chain from Geth
---

When coming from Geth, be aware that the `--geth` option provides you with the highest degree of compatibility to Geth's behaviour, even at the cost of speed, security, functionality and standardisation.

### Parity version 1.3 and above (Snapshot sync)

If you don't care about historical transaction information, just use our [state-snapshotting](Getting-Synced.md) to get synced really fast.

### Parity version 1.2 and above

On 1.2 and above, use Geth's export feature along with Parity's import:

```bash
mkfifo /tmp/chain.rlp && geth export /tmp/chain.rlp &

sleep 1 && parity import /tmp/chain.rlp
```

