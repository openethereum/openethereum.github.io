As of version 1.2, Parity works with the Ethereum Foundation Wallet ("Mist").

To use Parity with Mist, you need to make sure Parity is running in Geth-IPC-compatibility mode; to do this just run with the `--geth` option (older versions of 1.2 should use `--ipc-path=$HOME/.ethereum/geth.ipc`). E.g.:

```
parity --geth
```

Once it's happily churning away on the network, just run the Ethereum Wallet as you would otherwise and it'll magically use your Parity installation.