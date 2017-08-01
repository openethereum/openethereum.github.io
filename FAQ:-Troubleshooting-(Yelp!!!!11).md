### Double-clicking the Parity icon doesn't do anything!?!

Probably your node is already running (or another node is running, i.e., Geth) and another instance of Parity can not be launched with the same configuration (ports, dapps, web interface, etc.).

### How can I make Parity to write logs?

You can control the logging level with `-l` and define a `--log_file` location:

```bash
  -l --logging LOGGING           Specify the logging level. Must conform to the same
                                 format as RUST_LOG. (default: None)
  --log-file FILENAME            Specify a filename into which logging should be
                                 appended. (default: None)
```

See also: [How to make Parity write logs](http://ethereum.stackexchange.com/questions/3331/how-to-make-parity-write-logs)?

### Where are the logs when Parity runs in daemon mode?

Use

```bash
parity daemon --log-file /path/to/parity.log
```

See also: [Where are the parity log files in daemon mode](http://ethereum.stackexchange.com/questions/11363/where-are-the-parity-log-files-in-daemon-mode)?

### How can I report an issue with Parity?

If you have any urgent matters, get in [touch with us on Gitter](https://gitter.im/ethcore/Parity). If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/paritytech/Parity/issues/new).

### I just scrolled all the way down here and didn't find what I'm looking for!?!

Find more questions [tagged with `[Parity]` on Ethereum Stack Exchange]() or [ask a new question](https://ethereum.stackexchange.com/questions/ask).

However, if you have any urgent matters, get in [touch with us on Gitter](https://gitter.im/ethcore/Parity). If you run into issues with Parity, please consider [creating a ticket on Github](https://github.com/paritytech/Parity/issues/new).
