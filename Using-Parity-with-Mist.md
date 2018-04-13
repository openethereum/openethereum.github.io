---
title: Using Parity with Mist
---

As of version 1.2, Parity works with the [Ethereum Foundation Wallet ("Mist")](https://github.com/ethereum/mist/releases).

To use Parity with Mist, you need to make sure Parity is running in Geth-IPC-compatibility mode; to do this just run with the `--geth` option. For example:

```
parity --geth
```

Once it's happily churning away on the network, just run the Ethereum Wallet as you would otherwise and it'll magically use your Parity installation.

### Troubleshooting

#### Blank window

If you're running the wallet and seeing only a blank white/black window, your GPU is probably on the blacklist. One workaround is to run Mist with no blacklist:

```
./Ethereum-Wallet --ignore-gpu-blacklist
```

