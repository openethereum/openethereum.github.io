---
title: Demo PoA-Tutorial
---

This tutorial will walk through setting up two OpenEthereum nodes locally and sending transactions between them. If you want to learn more about different parameters specified in this tutorial please refer to the [Chain specification](Chain-specification) page.

Each node will act as an authority on the network issuing blocks when necessary, there will be also one additional user account with a high initial balance. All files produced in this tutorial can be found [here](https://github.com/keorn/parity-poa-tutorial).

## 1. Choose your chain

We will run a chain with Authority Round consensus engine. First we need to create a basic chain spec with all required fields.

```json
{
    "name": "DemoPoA",
    "engine": {
        "authorityRound": {
            "params": {
                "stepDuration": "5",
                "validators" : {
                    "list": []
                }
            }
        }
    },
    "params": {
        "gasLimitBoundDivisor": "0x400",
        "maximumExtraDataSize": "0x20",
        "minGasLimit": "0x1388",
        "networkID" : "0x2323",
        "eip155Transition": 0,
        "validateChainIdTransition": 0,
        "eip140Transition": 0,
        "eip211Transition": 0,
        "eip214Transition": 0,
        "eip658Transition": 0
    },
    "genesis": {
        "seal": {
            "authorityRound": {
                "step": "0x0",
                "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
            }
        },
        "difficulty": "0x20000",
        "gasLimit": "0x5B8D80"
    },
    "accounts": {
        "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
        "0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
        "0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
        "0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } }
    }
}
```

`"gasLimitBoundDivisor"` which determines gas limit adjustment has the usual Ethereum value
`"stepDuration"` is set to at least 5 second block times
`"validators"` is empty right now since we are yet to create our authority accounts

`"params"` standard for most chains

`"genesis"` has some standard values for the Authority Round consensus

`"accounts"` contains the standard Ethereum builtin contracts, these should be included to use the Solidity contract writing language

Save the above under `demo-spec.json`.

## 2. Setting up the two nodes

Now that a bare bones chain specification is complete the two nodes can be set up. OpenEthereum stores accounts for each chain with a different genesis hash in a separate folder, so in order to create the accounts in the correct one we will need to run with the `--chain` option.
Normally the two nodes would be started on separate machines, however since we are using the same one we will need to resolve some possible collisions:
- `-d` determines the directory that a OpenEthereum instance uses for data and keys
- `--port` determines the port via which OpenEthereum communicates with other nodes
- `--jsonrpc-port` is the RPC port
- `--ws-port` is the port used by the wallet to talk to the node
We will also want to expose all RPC apis to make interacting with the nodes easier `--jsonrpc-apis web3, eth, net, personal, parity, parity_set, traces, rpc, parity_accounts`.

Putting it all together gives us the following command to start OpenEthereum:
```
openethereum  --chain demo-spec.json -d /tmp/parity0 --port 30300 --jsonrpc-port 8540 --ws-port 8450 --jsonrpc-apis web3,eth,net,personal,parity,parity_set,traces,rpc,parity_accounts
```

Since the command is becoming rather clunky we can use a [config files](Configuring-OpenEthereum#Config-File) instead, which are passed using `--config` option. Node 0 will have this config file saved under `node0.toml`:
```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity0"
[network]
port = 30300
[rpc]
port = 8540
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[websockets]
port = 8450
```

While node 1 will have config saved under `node1.toml`:
```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity1"
[network]
port = 30301
[rpc]
port = 8541
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[websockets]
port = 8451
[ipc]
disable = true
```

We will create three accounts: two authorities and one user account. There are different methods to create accounts, pick one that suits you best:
### Method 1. Using RPC
Start the node 0 using `openethereum --config node0.toml`.

RPC can accessed via `web3`, `parity.js` or simply using `curl`. This will create the first authority address:
```
curl --data '{"jsonrpc":"2.0","method":"parity_newAccountFromPhrase","params":["node0", "node0"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8540
```
Returned address should be `0x00bd138abd70e2f00903268f3db08f2d25677c9e`.

The user address:
```
curl --data '{"jsonrpc":"2.0","method":"parity_newAccountFromPhrase","params":["user", "user"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8540
```
Returned address should be `0x004ec07d2329997267ec62b4166639513386f32e`.

Now start the other node with `openethereum --config node1.toml` and create the second authority account:
```
curl --data '{"jsonrpc":"2.0","method":"parity_newAccountFromPhrase","params":["node1", "node1"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8541
```
Returned address should be `0x00aa39d30f0d20ff03a22ccfc30b7efbfca597c2`.

### Method 2. Using command `openethereum account new`
You can also create an account without starting OpenEthereum using:
```
openethereum account new --config node0.toml
```
This does not give you control over what address will be created, so for the remainder of the tutorial we will stick with the accounts created by the previous methods.

## 3. Complete the chain specification

If the account were created from phrases we should have the following setup now:
- node 0 has the `0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e` authority account and `0x004ec07d2329997267Ec62b4166639513386F32E` user account
- node 1 has the `0x00Aa39d30F0D20FF03a22cCfc30B7EfbFca597C2` authority account

The accounts can be now added to the spec file. Open `demo-spec.json` back up and add the authorities we have just created to the `"validators"` array:
```
"validators" : {
    "list": [
        "0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e",
        "0x00Aa39d30F0D20FF03a22cCfc30B7EfbFca597C2"
    ]
}
```

Then add our user account to the genesis, so that we have some balance to send around:
```
"0x004ec07d2329997267Ec62b4166639513386F32E": { "balance": "10000000000000000000000" }
```

The complete `demo-spec.json` should look like this:
```
{
    "name": "DemoPoA",
    "engine": {
        "authorityRound": {
            "params": {
                "stepDuration": "5",
                "validators" : {
                    "list": [
                        "0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e",
                        "0x00Aa39d30F0D20FF03a22cCfc30B7EfbFca597C2"
                    ]
                }
            }
        }
    },
    "params": {
        "gasLimitBoundDivisor": "0x400",
        "maximumExtraDataSize": "0x20",
        "minGasLimit": "0x1388",
        "networkID" : "0x2323",
        "eip155Transition": 0,
        "validateChainIdTransition": 0,
        "eip140Transition": 0,
        "eip211Transition": 0,
        "eip214Transition": 0,
        "eip658Transition": 0
    },
    "genesis": {
        "seal": {
            "authorityRound": {
                "step": "0x0",
                "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
            }
        },
        "difficulty": "0x20000",
        "gasLimit": "0x5B8D80"
    },
    "accounts": {
        "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
        "0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
        "0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
        "0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
        "0x004ec07d2329997267Ec62b4166639513386F32E": { "balance": "10000000000000000000000" }
    }
}
```

## 4. Run the authority nodes

Now that the spec is complete we can start the two nodes that will be running the chain. To run a node as an authority we will need to enable it to sign the consensus messages.

First lets create a file with the node passwords `node.pwds`. Each line will contain a password that we used when creating the authority accounts, contents to be stored in file named `node.pwds` are:

```
node0
node1
```

Now we can add `engine-signer` to our config files. `node0.toml`:

```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity0"
[network]
port = 30300
[rpc]
port = 8540
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[websockets]
port = 8450
[account]
password = ["node.pwds"]
[mining]
engine_signer = "0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e"
reseal_on_txs = "none"
```
and `node1.toml`:
```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity1"
[network]
port = 30301
[rpc]
port = 8541
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[websockets]
port = 8451
[ipc]
disable = true
[account]
password = ["node.pwds"]
[mining]
engine_signer = "0x00Aa39d30F0D20FF03a22cCfc30B7EfbFca597C2"
reseal_on_txs = "none"
```

The two nodes can now be started. Node 0:
```
openethereum --config node0.toml
```
and node 1:
```
openethereum --config node1.toml
```

## 5. Connecting the nodes

In order to ensure the nodes are connected to each other we will need to know their [enode addresses](https://github.com/ethereum/wiki/wiki/enode-url-format) and inform the other node about it. There are two ways to obtain the enode:
- RPC
- startup console output

Once obtained they can be added either as boot nodes in the `demo-spec.json` or as reserved peers.

Here we will simply use `curl`. Obtain node 0 enode:
```
curl --data '{"jsonrpc":"2.0","method":"parity_enode","params":[],"id":0}' -H "Content-Type: application/json" -X POST localhost:8540
```
Add the "result" to node 1 (replace `enode://RESULT` in the command):
```
curl --data '{"jsonrpc":"2.0","method":"parity_addReservedPeer","params":["enode://RESULT"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8541
```

Now the nodes should indicate `0/1/25 peers` in the console, which means they are connected to each other.

## 6. Send transactions

### Using RPC
Send some tokens from the user account to authority node0:
```
curl --data '{"jsonrpc":"2.0","method":"personal_sendTransaction","params":[{"from":"0x004ec07d2329997267Ec62b4166639513386F32E","to":"0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e","value":"0xde0b6b3a7640000"}, "user"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8540
```
Once the request is submitted a block should be issued a few seconds later. You can check that the other account received the funds:
```
curl --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e", "latest"],"id":1}' -H "Content-Type: application/json" -X POST localhost:8540
```
We can also send some to the account that is on the other node:
```
curl --data '{"jsonrpc":"2.0","method":"personal_sendTransaction","params":[{"from":"0x004ec07d2329997267Ec62b4166639513386F32E","to":"0x00Aa39d30F0D20FF03a22cCfc30B7EfbFca597C2","value":"0xde0b6b3a7640000"}, "user"],"id":0}' -H "Content-Type: application/json" -X POST localhost:8540
```
and check if it was received asking the other node:
```
curl --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x00Aa39d30F0D20FF03a22cCfc30B7EfbFca597C2", "latest"],"id":1}' -H "Content-Type: application/json" -X POST localhost:8541
```

## 7. Further development

You can now create more accounts, send value around, write contracts and deploy them. All the tools that are used to develop and use the Ethereum network can be also used in this network.

To deploy OpenEthereum on multiple machines you may find the [docker builds](Docker) useful.

To add a non-authority node this simpler config can be used:
```
[parity]
chain = "demo-spec.json"
base_path = "/tmp/parity2"
[network]
port = 30302
[rpc]
port = 8542
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
[websockets]
port = 8452
[ipc]
disable = true
```
Then the accounts and connecting the nodes can be done the same as for the authority nodes. To make sure that transactions are accepted, authorities can also run with `usd_per_tx = "0"` field under `[mining]`. Any node submitting a transaction will be then able to do so for free.

When running nodes on separate machines most of the fields will be redundant, so the basic config will only have the chain and possibly the RPC apis:
```
[parity]
chain = "demo-spec.json"
[rpc]
apis = ["web3", "eth", "net", "personal", "parity", "parity_set", "traces", "rpc", "parity_accounts"]
```
or simply running `openethereum --chain demo-spec.json`!
