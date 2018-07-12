---
title: Network Configuration
---

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

## Reduce the bandwidth

If browsing the internet or generally using it is slow with Parity running, then to use the internet normally again, you can download and install the Trickle program, e.g. via [here](https://trickle.en.uptodown.com/ubuntu) or its repo [here](https://github.com/mariusae/trickle), and run:
```
$ trickle -h
Usage: trickle [-hvVs] [-d <rate>] [-u <rate>] [-w <length>] [-t <seconds>]
               [-l <length>] [-n <path>] command ...
	-h           Help (this)
	-v           Increase verbosity level
	-V           Print trickle version
	-s           Run trickle in standalone mode independent of trickled
	-d <rate>    Set maximum cumulative download rate to <rate> KB/s
	-u <rate>    Set maximum cumulative upload rate to <rate> KB/s
	-w <length>  Set window length to <length> KB 
	-t <seconds> Set default smoothing time to <seconds> s
	-l <length>  Set default smoothing length to <length> KB
	-n <path>    Use trickled socket name <path>
	-L <ms>      Set latency to <ms> milliseconds
	-P <path>    Preload the specified .so instead of the default one
```

You can set the global settings for Trickle with `trickled`, adjusting the values for the options according to your internet metrics. Setting the global settings with `trickled` saves having to specify the options every time you run `trickle`. For instance, if you have a 12 Mbps download speed and a 1 Mbps upload speed, you could try `trickled -d 300 -u 25`, then run `trickle parity`. (1 KB = 0.008 Mbits.) You may want to tweak the values of these two options (and maybe others, e.g. `-w`, `-t`, `-l` and `-L`). For instance with a 20 Mbps max. speed, you could try:

```
trickled -d 950 -u 165 -t 0.2 -l 20 -w 256
trickle parity
```
And if you don't need to limit the bandwidth to browse or run other applications, such as when you are away from the computer or overnight, you can just run `parity`.
