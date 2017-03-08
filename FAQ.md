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

##### I'm getting build errors?
Make sure you are using the latest stable rust compiler. Update with `rustup update` when using rustup or `multirust update` when using multirust.

##### How to perform a cross-build on ARM/Raspberry Pi?
See the documentation for [docker build for ARM](https://github.com/ethcore/parity-snappy/wiki/Docker-build-for-ARM-ARM64).

##### Trying to build on Linux, I get libudev or libssl related errors?
Install required packages `libudev-dev` and `libssl-dev`.

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

##### How to delete blockchain (to initiate full re-sync) without deleting wallet info?

*For Parity 1.5 and above:*

Execute `parity db kill`.
Relevant options are `--chain=*` `--pruning=*` and `--db-path/--base-path`.
I.e. to delete `morden` run:
```bash
parity db kill --chain=morden
```

*For Parity 1.4 and below:*

Execute `rm -rf ~/.parity/906a34e69aec8c0d`
This will remove the local chain database.
Always back up your keys (~/.parity/keys) before doing anything like that though.

##### What can I do when Parity has trouble getting in sync?
Parity is running but seems to remain at the same block for a long time.

1. Make sure you have up to date version of Parity.
2. Go to http://time.is/ and ensure it says "Your time is exact":
   
   ![image](https://cloud.githubusercontent.com/assets/138296/19265409/5e4a89ce-8fa5-11e6-8ec6-6c72c138ee48.png)
   
   If it isn't get it synced. Your machine will not automatically do this; my machine is routinely 1-2 seconds out of sync which is enough to disrupt peer discovery.

3. Make sure your network does not block UDP traffic.
4. Remove your nodes file (`$HOME/.parity/network/nodes.json`) while Parity is not running.
5. Wait a bit longer.

If the problem persists, please run Parity with `-l sync=trace` option for a while and post the output [here](https://github.com/ethcore/parity/issues/2447).

##### Syncing is stuck around block 2,421,000 or 2,675,000

These blocks are quite heavy on the IO and take a long time to import, especially on machines with HDD. Consider using `--warp` or switching to SSD drive.

##### Parity doesn't start on Windows; fails with "api-ms-win-crt-math-l1-1-0.dll missing".
![parity_error](https://cloud.githubusercontent.com/assets/2982011/18855854/1d3b2c22-8424-11e6-8329-1dbe5edf6a5a.png)

Install the following windows update: https://support.microsoft.com/en-us/kb/2999226

##### How do I enable pruning?
Since Parity 1.2 state-trie pruning is enabled by default.

##### How do I enable tracing?
Transaction tracing, available in Parity 1.1 and above, enables all `CALL`/`CREATE` information (i.e. "internal transactions") to be efficiently searched and filtered. More information can be found on the dedicated [Wiki page](JSONRPC-trace-module).

#### Backup, Restore and Files

##### Where can I find Parity's local files?

On Windows blockchain data is located in `/Users/You/AppData/Local/Parity/Ethereum`
while keys and configuration are kept in `/Users/You/AppData/Roaming/Parity/Ethereum`

On MacOS: `/Users/you/Library/Application Support/io.parity.ethereum`

On Linux: `/home/you/.local/share/io.parity.ethereum`


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

*Parity 1.5 and above:*
Simply use Parity Wallet's "New Account" function on the "Accounts" page.

*Parity 1.4 and below:*
Simply copy it into your `$HOME/.parity/keys` directory. Parity will import all keys found there.

#### Mining and Transacting

##### None of my JSONRPC requests work - they all fail with no output. WTF?
As a safeguard against a certain type of attack, Parity requires you to use a special content type. Just add `-H "Content-Type: application/json"` to your `curl` command.

##### When I start Parity with the `homestead_test.json` I get a 0 block chain, but loading `ethminer` never mines a block.
`homestead_test.json` is a file meant for testing on a chain very similar to the Homestead chain. As such it is of equivalent (substantial) difficulty to mine on it. Rather what you need is something of minimal mining difficulty, like Morden. See more instructions [here](https://github.com/ethcore/parity/wiki/Private-chains).

##### Why do my transactions not get mined?
Your gas price is probably too low. Try increasing the gas price to 20Gwei by appending `, gasPrice: 20000000000` into your transaction spec or `sendTransaction` options.

##### All my `eth_sendTransaction` calls only return "0x00000000...000000000" as a return value.
You're probably trying to send from a locked account. Ensure you use the `--unlock` (to specify which account(s) to unlock) and `--password` (to pass in the file contain any passwords needed to unlock those acocunts) so that the account(s) from which you're sending the transaction are unlocked.