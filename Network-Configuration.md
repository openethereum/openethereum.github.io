# Network Configuration

Parity utilizes the network aggressively which allows finding and connecting to peers quickly. Default networking strategy designed for universal connectivity across LAN and WAN networks may trigger false positive alarms with the security software and service providers. This page contains a number of tips to optimize bandwidth usage and configure Parity to be more hosting provider friendly.

## Configure network IP range

By default Parity tries to connect to all possible node addresses learned from the discovery message. This includes both local and public IPs. If you are running a single node that is only facing the internet it is a good idea to prevent any attempts to connect to LAN peers. Use `--allow-ips=public` CLI flag to achieve that. On the other hand if you want parity to connect to local network peers only use `--allow-ips=private`

## Disable discovery

If your node can accept incoming connections you might want to disable UDP traffic completely with `--no-discovery`. Other peers will find your node anyway.

## Limit pending connections

Overall network connection rate can be further reduced with `--max-pending-peers` option. If your security software detects an attack because of too high connection rate try reducing that to 32 or less.

## Reduce maximum number of peers.

Safe minimum of connected peers is 10. You can reduce the number of peers that Parity connects to with `--max-peers` to 10 and still get good connectivity in most cases saving on the traffic.

## Summary

If you run an internet facing node that can accept incoming connections and you get abuse complaints from your hosting provider use `--allow-ips=public --no-discovery` configuration options.
