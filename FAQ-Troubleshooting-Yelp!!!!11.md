---
title: FAQ - Troubleshooting (Yelp!!!!11)
---

## Double-clicking the Parity icon doesn't do anything!?!

Probably your node is already running (or another node is running, i.e., Geth) and another instance of Parity cannot be launched with the same configuration (ports, dapps, web interface, etc.).

## How can I make Parity to write logs?

You can control the logging level with `-l` and define a `--log_file` location:

```bash
  -l --logging LOGGING           Specify the logging level. Must conform to the same
                                 format as RUST_LOG. (default: None)
  --log-file FILENAME            Specify a filename into which logging should be
                                 appended. (default: None)
```
LOGGING accepts the following modules:
`account_bloom`,`basicauthority`,`blockchain`,`chain`,`client`,`dapps`,`discovery`,`diskmap`,`enact`,`engine`,`estimate_gas`,`ethash`,`executive`,`ext`,`externalities`,`external_tx`,`fatdb`,`fetch`,`finality`,`hypervisor`,`jdb`,`jdb.fine`,`jdb.ops`,`journaldb`,`les`,`light_fetch`,`local_store`,`migration`,`miner`,`mode`,`network`,`on_demand`,`own_tx`,`perf`,`pip`,`pip_provider`,`poa`,`pubsub`,`rcdb`,`secretstore`,`secretstore_net`,`shutdown`,`signer`,`snapshot`,`snapshot_io`,`snapshot_watcher`,`spec`,`state`,`stats`,`stratum`,`sync`,`trie`,`txqueue`,`updater`,`wasm`,`whisper`

Additionally, a logging level can  be set per module:
`info`, `debug`, `trace` (default if no level is specified for a module)

Examples:
- `-l sync=debug` prints the debug level logs for the sync module only.
- `-l sync,snapshot,txqueue` prints the logs for sync, snapshot, and txqueue with trace level.
- `-l sync=info,snapshot=debug,txqueue=trace` prints the info level logs for the sync module, the debug level of logs for the snapshot module etc.

Note that the info logging level of all modules will always be printed, equivalent to `-l info`.

See also: [How to make Parity write logs](http://ethereum.stackexchange.com/questions/3331/how-to-make-parity-write-logs)?

## Where are the logs when Parity runs in daemon mode?

Use

```bash
parity daemon --log-file /path/to/parity.log
```

See also: [Where are the parity log files in daemon mode](http://ethereum.stackexchange.com/questions/11363/where-are-the-parity-log-files-in-daemon-mode)?

## How can I report an issue with Parity?

If you have any urgent matters, get in [touch with us on Gitter](https://gitter.im/paritytech/Parity). If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/paritytech/Parity/issues/new).

## I just scrolled all the way down here and didn't find what I'm looking for!?!

Find more questions [tagged with `[Parity]` on Ethereum Stack Exchange]() or [ask a new question](https://ethereum.stackexchange.com/questions/ask).

However, if you have any urgent matters, get in [touch with us on Gitter](https://gitter.im/paritytech/Parity). If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/paritytech/Parity/issues/new).
