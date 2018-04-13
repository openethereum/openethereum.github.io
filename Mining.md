---
title: Mining
---

Parity supports standard Ethereum JSON-RPC interface for mining ([eth_getWork](JSONRPC-eth-module.md#eth_getwork), [eth_submitWork](JSONRPC-eth-module.md#eth_submitwork) methods) and thus compatible with any miner which implements Ethereum Proof-of-Work.

First get a Parity node up and running (either build yourself or install one of the packages; the [Setup](Setup.md) guide can help you). Next, you'll need to install your preferred miner.

## Getting the miner
### ethminer

Just follow the instruction on the Ethereum-Mining GitHub repository page: https://github.com/ethereum-mining/ethminer.

### sgminer

Follow instructions on the sgminer github repository page: https://github.com/sgminer-dev/sgminer

## Starting it

You'll probably want to set the destination address (where the rewards go). If you have an address already, great. If you don't, then you can make one in Parity with:

```
parity account new
```

You'll be asked for a password and be given an address. Once done, you should run parity and tell it to mine to that address when required. Supposing your address is `0037a6b811ffeb6e072da21179d11b1406371c63`, then you would run:

```
parity --author 0037a6b811ffeb6e072da21179d11b1406371c63
```

Once Parity is running and synced with the network, you can start your miner of choice. For genoil, command would be:
```
ethminer -G
```
Where `-G` for GPU mining (opencl)

## Stratum

Since 1.6, Parity offers Stratum protocol support (beta). This protocol has number of advantages compared to the JSON-RPC polling, which results in better hashrate and less node strain. To run parity with Stratum on, use

```
parity --author 0037a6b811ffeb6e072da21179d11b1406371c63 --stratum
```

By default Stratum listens on local network interface (`127.0.0.1`) and port `8008`. You can change it by providing `--stratum-interface` and `--stratum-port` arguments, respectively. For example, to use all network interfaces on the local machine and listen on port 9009:

```
parity --author 0037a6b811ffeb6e072da21179d11b1406371c63 --stratum --stratum-interface=0.0.0.0 --stratum-port=9009
```

To utilize Stratum, miner also needs to be started in Stratum mode. For genoil, it will be
```
ethminer -G -S 127.0.0.1:8008
```

## HTTP Notification

Parity can push work package notifications to a set of URLs by using the option `--notify-work URLS`, where URLS should be a comma-delimited list of HTTP URLs. For example,

```
parity --author 0037a6b811ffeb6e072da21179d11b1406371c63 --notify-work https://myhttpserver.local/workpackage
```

Once new work package becomes available, Parity will make a HTTP POST request on the provided url. Body will contain JSON-serialized array of work package data.
