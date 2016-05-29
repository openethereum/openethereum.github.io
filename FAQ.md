###### How do I build and install?
Once you have the source directory and Rust installed, just use cargo and copy the resulting binary:

```bash
cargo build --release && cp target/release/parity /usr/local/bin
```

###### Why doesn't `--geth` option work?
It's only available on version 1.2 and above. Check `parity --version` to make sure you're not on an older version.