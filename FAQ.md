#### Building Parity

###### How do I build the cutting-edge version of Parity?
Once you have cloned the source directory and installed Rust, just use cargo and copy the resulting binary:
```bash
cargo build --release
```
You can install the binary by `cp`ing (`copy`ing on Windows) `target/release/parity` to a system path (`/usr/local/bin` on Linux/Mac, `C:\Windows` on Windows).

###### How do I do a cross-build for ARM/Raspberry Pi?
See the documentation for [docker build for ARM](https://github.com/ethcore/parity-snappy/wiki/Docker-build-for-ARM-ARM64).

###### Trying to build on Windows, I get `link failed` mentioning `kernel32.lib`?
Make sure you have set up your 64-bit variables in the build shell before issuing `cargo build`:

````
"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64\vcvars64.bat"
````

###### How do I build and run tests?
First make sure you have the cross-client consensus tests installed:
```
git submodule init
git submodule update
```
Then you just run `./test.sh`. You can run individual tests with `cargo test -p <package-name>`.


#### Backup, Restore and Files

###### Where can I find Parity's local files in Windows?
In Windows, the $HOME parameter is equivalent to C:\Users\{CurrentUser}. Parity's local files live in $HOME/.parity

###### How do I backup my blockchain?
You can just export it to a file:
```bash
parity export $HOME/ethereum-chain-backup.rlp
```

###### How do I restore my blockchain from a previous backup?
Just use `import`:
```bash
parity import $HOME/ethereum-chain-backup.rlp
```

###### How do I backup/restore my keys?
See [[Backing-up-&-Restoring]].

#### Coming from Geth

###### Why doesn't `--geth` option work?
It's only available on version 1.2 and above. Check `parity --version` to make sure you're not on an older version.

###### How do I import my chain from Geth?
See [[Importing a Chain from Geth]].

###### How do I import my keys from Geth?
They get imported automatically.

###### How do I import a key made outside of Geth? (e.g. myetherwallet.com)
Simply copy it into your `$HOME/.ethereum/keystore` directory. Parity will import all keys found there.

#### Mining and Transacting

###### None of my JSONRPC requests work - they all fail with no output. WTF?
As a safeguard against a certain type of attack, Parity requires you to use a special content type. Just add `-H "Content-Type: application/json"` to your `curl` command.

###### When I start Parity with the `homestead_test.json` I get a 0 block chain, but loading `ethminer` never mines a block.
`homestead_test.json` is a file meant for testing on a chain very similar to the Homestead chain. As such it is of equivalent (substantial) difficulty to mine on it. Rather what you need is something of minimal mining difficulty, like Morden. See more instructions [here](https://github.com/ethcore/parity/wiki/Private-chains).

###### Why do my transactions not get mined?
Your gas price is probably too low. Try increasing the gas price to 20Gwei by appending `, gasPrice: 20000000000` into your transaction spec or `sendTransaction` options.

###### All my `eth_sendTransaction` calls only return "0x00000000...000000000" as a return value.
You're probably trying to send from a locked account. Ensure you use the `--unlock` (to specify which account(s) to unlock) and `--password` (to pass in the file contain any passwords needed to unlock those acocunts) so that the account(s) from which you're sending the transaction are unlocked.