#### Building Parity

##### How do I build the cutting-edge version of Parity?
Once you have cloned the source directory and installed Rust, just use cargo and copy the resulting binary:
```bash
cargo build --release
```

##### How do I install Parity after building?
- On Windows: `copy target/release/parity C:/Windows`
- On Mac/Linux: `cp -f target/release/parity /usr/local/bin`

##### How do I upgrade Parity to the latest cutting-edge version?
Simply follow the instructions above to build & install. The new version will replace the old. Nothing additional is needed.

##### How do I do a cross-build for ARM/Raspberry Pi?
See the documentation for [docker build for ARM](https://github.com/ethcore/parity-snappy/wiki/Docker-build-for-ARM-ARM64).

##### Trying to build on Windows, I get `link failed` mentioning `kernel32.lib`?
Make sure you have set up your 64-bit variables in the build shell before issuing `cargo build`:

````
"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64\vcvars64.bat"
````

##### How do I build and run tests?
First make sure you have the cross-client consensus tests installed:
```
git submodule init
git submodule update
```
Then you just run `./test.sh`. You can run individual tests with `cargo test -p <package-name>`.

#### Basic Operation

##### How do I enable pruning?
State-trie pruning was introduced in Parity 1.1 and is considered in a beta testing stage, but is not yet considered production-ready. To enable it, start `parity` using `--pruning=fast` for your first sync.

For Parity 1.2, pruning is enabled by default.

##### How do I enable tracing?
Transaction tracing, available in Parity 1.1 and above, enables all `CALL`/`CREATE` information (i.e. "internal transactions") to be efficiently searched and filtered. To enable it, start `parity` using `--tracing` for your first sync.

#### Backup, Restore and Files

##### Where can I find Parity's local files?
In Windows, the `$HOME` parameter is equivalent to `C:\Users\{CurrentUser}`. Parity's local files live in `$HOME/.parity` making it `C:\Users\{CurrentUser}\.parity`.

##### How do I backup my blockchain?
You can just export it to a file:
```bash
parity export $HOME/ethereum-chain-backup.rlp
```

##### How do I restore my blockchain from a previous backup?
Just use `import`:
```bash
parity import $HOME/ethereum-chain-backup.rlp
```

##### How do I backup/restore my keys?
See [[Backing-up-&-Restoring]].

#### Coming from Geth

##### Why doesn't `--geth` option work?
It's only available on version 1.2 and above. Check `parity --version` to make sure you're not on an older version.

##### How do I import my chain from Geth?
See [[Importing a Chain from Geth]].

##### How do I import my keys from Geth?
They get imported automatically.

##### How do I import a key made outside of Geth? (e.g. myetherwallet.com)
Simply copy it into your `$HOME/.ethereum/keystore` directory. Parity will import all keys found there.

#### Mining and Transacting

##### None of my JSONRPC requests work - they all fail with no output. WTF?
As a safeguard against a certain type of attack, Parity requires you to use a special content type. Just add `-H "Content-Type: application/json"` to your `curl` command.

##### When I start Parity with the `homestead_test.json` I get a 0 block chain, but loading `ethminer` never mines a block.
`homestead_test.json` is a file meant for testing on a chain very similar to the Homestead chain. As such it is of equivalent (substantial) difficulty to mine on it. Rather what you need is something of minimal mining difficulty, like Morden. See more instructions [here](https://github.com/ethcore/parity/wiki/Private-chains).

##### Why do my transactions not get mined?
Your gas price is probably too low. Try increasing the gas price to 20Gwei by appending `, gasPrice: 20000000000` into your transaction spec or `sendTransaction` options.

##### All my `eth_sendTransaction` calls only return "0x00000000...000000000" as a return value.
You're probably trying to send from a locked account. Ensure you use the `--unlock` (to specify which account(s) to unlock) and `--password` (to pass in the file contain any passwords needed to unlock those acocunts) so that the account(s) from which you're sending the transaction are unlocked.

#### Testnet & Private Chains

##### When I switched to a testnet, my main chain wallets still appear. Might it be unsafe?
Parity does not ([yet](https://github.com/ethcore/parity/issues/1221)) bother distinguishing keystores between the different networks. To avoid potential cross-network transaction-replay attacks, ensure all private networks have a reasonably high number for the `accountStartNonce` parameter. Morden has the value of `"0x0100000"`, which is considered safe.

Note, the security of `--unlock` for an active mainnet account is unchanged, regardless of which network you are on. You are heavily discouraged from ever using `--unlock` and instead use the [Secure Transaction Signer](https://github.com/ethcore/parity/wiki/Secure-Transaction-Signer).