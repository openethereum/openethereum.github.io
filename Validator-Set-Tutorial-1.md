---
title: Configuring each node
---

In this first part, we will create all the files needed to bootstrap a local PoA based network. This includes creating the chain specification file (also called genesis) as well as the node's configuration files for `Node0`, `Node1` and `Alice`.

The workflow of this tutorial is the following:
- Using a fixed list of authorities, `Node0` will be the only authority allowed to seal blocks at the genesis of the blockchain.
- `Alice` will deploy a Validator Set contract using [Remix](https://remix.ethereum.org). The contract will also only allow `Node0` to seal blocks.
- `Node0` will plan a fork to switch from a fixed list of validators to the Validator Set contract deployed previously by `Alice`.
- `Alice`, as the owner of the Validator Set contract will add `Node1` as a validator additionally to `Node0`.

Find [here](https://github.com/Tbaut/Validator-set-tutorial/tree/master/Part-1-files) the files that will be generated in the following sections.

## 1. Create the genesis file

All the nodes of this tutorial will run on an Aura-based blockchain. Create a directory to store all the configuration files at one place.
Create `Validator-set-tutorial.json` file containing:

```json
{
    "name": "Validator-set-tutorial",
    "engine": {
        "authorityRound": {
            "params": {
                "stepDuration": "5",
                "validators" : {
                    "multi": {
                        "0": {
                            "list": [""]
                        }
                    }
                }
            }
        }
    },
    "params": {
        "gasLimitBoundDivisor": "0x400",
        "maximumExtraDataSize": "0x20",
        "minGasLimit": "0x7A1200",
        "networkID" : "0x2323",
        "eip155Transition": 0,
        "validateChainIdTransition": 0,
        "eip140Transition": 0,
        "eip211Transition": 0,
        "eip214Transition": 0,
        "eip658Transition": 0,
        "eip150Transition": "0x0",
        "eip160Transition": "0x0",
        "eip161abcTransition": "0x0",
        "eip161dTransition": "0x0",
        "eip98Transition": "0x7fffffffffffff",
        "maxCodeSize": 24576,
        "maxCodeSizeTransition": "0x0"
    },
    "genesis": {
        "seal": {
            "authorityRound": {
                "step": "0x0",
                "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
            }
        },
        "difficulty": "0x20000",
        "gasLimit": "0x7A1200"
    },
    "accounts": {
        "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
        "0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
        "0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
        "0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
        "0x0000000000000000000000000000000000000005": { "builtin": { "name": "modexp", "activate_at": 0, "pricing": { "modexp": { "divisor": 20 } } } },
        "0x0000000000000000000000000000000000000006": { "builtin": { "name": "alt_bn128_add", "activate_at": 0, "pricing": { "linear": { "base": 500, "word": 0 } } } },
        "0x0000000000000000000000000000000000000007": { "builtin": { "name": "alt_bn128_mul", "activate_at": 0, "pricing": { "linear": { "base": 40000, "word": 0 } } } },
        "0x0000000000000000000000000000000000000008": { "builtin": { "name": "alt_bn128_pairing", "activate_at": 0, "pricing": { "alt_bn128_pairing": { "base": 100000, "pair": 80000 } } } }
    }
}
```

This genesis file is defining a network called `Validator-set-tutorial` with many parameters similar to the ones the Kovan Network has. You are encouraged to visit [Aura consensus](Pluggable-Consensus#aura) as well as the [general chain specifications](Chain-specification) pages to understand the parameters used here. This genesis file is incomplete as we miss `Node0`'s address in the list of validators. We will create it in the next steps.

## 2. Create and configure the validators' nodes

### 2.1 Create and configure `Node0`
Each node corresponds to an account. The authorities (`Node0` at the beginning, then also `Node1`) seal blocks and sign them using their private key. `Alice` will send transactions and sign them as well. We will create an account for each of them. 

All nodes will run on a single machine. We will need to handle the networking ports carefully to avoid conflicts.

To keep a better overview, we will use TOML files to handle each node's configuration.

Create the file `node0.toml` with the following content:

```toml
# File node0.toml

[parity]
chain = "Validator-set-tutorial.json"
base_path = "node0"

[network]
port = 30300
#bootnodes = []

[rpc]
disable = true

[websockets]
disable = true

[account]
#password = [""]

[mining]
#engine_signer = ""
reseal_on_txs = "none"
force_sealing = true
```
At this point in the tutorial, we miss the address and password file for `Node0`'s account as well as the bootnodes' addresses corresponding to all the nodes that we will use in this tutorial. These fields are commented (notice the `#`) for the moment.
Let's create an account for `Node0` using the passwords: `node0pwd`.

To do this, simply run `openethereum --config ./node0.toml account new` using the passwords given above.
```bash
Loading config file from ./node0.toml
Please note that password is NOT RECOVERABLE.
Type password:    # type the password given above
Repeat password:  # type again 
0xa2922fec00bb29fe13d68e87f64b9ad1719ed64a
```
It will give you the address of the account we will use for `Node0`. 

We will need to create a password file also for the node to be able to sign messages and seal blocks using this account.

Run the following command to create a file named `node0.pwd` containing the password `node0pwd`:

```bash
echo "node0pwd" > node0.pwd
```

We will use this address and password file right away in our `node0.toml` configuration file. Add and uncomment the address as `engine_signer` in the `[mining]` section and add the password file in the `[account]` section as follow: 

```toml
[account]
password = ["node0.pwd"]

[mining]
engine_signer = "0xa2922fec00bb29fe13d68e87f64b9ad1719ed64a"
reseal_on_txs = "none"
force_sealing = true
```

### 2.2 Set `Node0` as validator in the genesis


As explained previously, we wish to have `Node0` as the unique validator for the beginning of the blockchain. 
Add `Node0`'s address to the `Validator-set-tutorial.json` genesis file in the validators' list so that it looks as follow:

```json
"validators" : {
    "multi": {
        "0": {
            "list": ["0xa2922fec00bb29fe13d68e87f64b9ad1719ed64a"]
        }
    }
}
```

This means that a fixed list of validators will be used from block 0. `Node0`'s address is the only one authorised to seal blocks.
To make sure the file is valid, launch `Node0` using the command `openethereum --config ./node0.toml` and verify that you do not get any error in the console.

You should see lines like the following every 5s:
```bash
Imported #1 0x4947…92ef (0 txs, 0.00 Mgas, 0 ms, 0.57 KiB)
```
This assesses that blocks are generated.

You should also copy in a text file the Public node address as we will use it later on. It looks like the following:
```bash
Public node URL: enode://dd87dbdb2ce7eec42cc4655a3e5abe12cdd0ede81b44f5492c84c892a9b209d91f8f0fb12fca44a5206711ae91d68d6bd643a1a018e3f59de30cd0415691b91e@172.18.0.1:30300
```


### 2.3 Create and configure `Node1`

The configuration for `Node1` looks very similar to the one from `Node0`.

Create the file `node1.toml` with the following content:

```toml
# File node1.toml

[parity]
chain = "Validator-set-tutorial.json"
base_path = "node1"

[network]
port = 30301
#bootnodes = []

[rpc]
disable = true

[websockets]
disable = true

[account]
#password = [""]

[mining]
#engine_signer = ""
reseal_on_txs = "none"
force_sealing = true
```
We are missing here also the address and password file for `Node1`'s account as well as the bootnodes' addresses corresponding to all the nodes that we will use in this tutorial. These fields are commented (notice the `#`) for the moment.
Let's create an account for `Node1` using the passwords: `node1pwd`.

To do this, simply run `openethereum --config ./node1.toml account new` using the passwords given above.
```bash
Loading config file from ./node1.toml
Please note that password is NOT RECOVERABLE.
Type password:    # type the password given above
Repeat password:  # type again 
0x32784591c38bf8aa081e96ba4db462bd73a3179e
```
It will give you the address of the account we will use for `Node1`. 

Although `Node1` will not initially be part of the validator's list, we can still create a password file and configure the node as if it was a validator. It will not seal blocks as long as the account signing them is not part of the authorised validators. It will start sealing as soon as that it is added to the Validator Set.

Run the following command to create a file named `node1.pwd` containing the password `node1pwd`:

```bash
echo "node1pwd" > node1.pwd
```

We will use this address and password file right away in our `node1.toml` configuration file. Add and uncomment the address as `engine_signer` in the `[mining]` section and add the password file in the `[account]` section as follow: 

```toml
[account]
password = ["node1.pwd"]

[mining]
engine_signer = "0x32784591c38bf8aa081e96ba4db462bd73a3179e"
reseal_on_txs = "none"
force_sealing = true
```

To make sure the file is valid, launch `Node1` using the command `openethereum --config ./node1.toml` and verify that you do not get any error in the console. As `Node1` and `Node0` might not be connected to each other yet, it is normal that `Node1` does not import any block from `Node0`.

Also, copy in a text file the Public node address as we will use is later on.
```bash
Public node URL: enode://70f8784653cb189b6e2daf925f25f1ca3dfad266370b6ddd258551e48159bd5b9c23bdd39367155136a0ee7d6331d4107c0a765850ccc706c37686baefb039a1@172.18.0.1:30301
```

### 3. Create and configure `Alice`'s node

`Alice`'s node configuration will be slightly different than the two validator's nodes that we have created previously.
It will not need any `[mining]` section. We will use the standard ports for HTTP and WebSockets RPCs so that `Alice`'s node can be easily connected to the tools used later such as Remix. 

Create the file `alice.toml` with the following content:

```toml
# File alice.toml

[parity]
chain = "Validator-set-tutorial.json"
base_path = "alice"

[network]
port = 30303
#bootnodes = []

[rpc]
port = 8545                             # Enable HTTP JSON RPC on the standard port
apis = ["eth","net"]
cors = ["https://remix.ethereum.org"]   # allow Remix to connect to this node

[websockets]
disable = false                         # Enable WS JSON RPC on the standard port
port = 8546
interface = "local"
```

We are missing here also the address and password file for `Alice`'s account as well as the bootnodes' addresses corresponding to all the nodes that we will use in this tutorial.
Let's create an account for `Alice` using the passwords: `alicepwd`.

To do this, simply run `openethereum --config ./alice.toml account new` using the passwords given above.
```bash
Loading config file from ./alice.toml
Please note that password is NOT RECOVERABLE.
Type password:    # type the password given above
Repeat password:  # type again 
0x12db1ee91481573302f63ebf3d735820081c68a2
```
It will give you the address of the account we will use for `Alice`. 

We will need to create a password file also for the account to be autonomously usable by the node.

Run the following command to create a file named `alice.pwd` and containing the password `alicepwd`:

```bash
echo "alicepwd" > alice.pwd
```

We will use this address and password file right away in our `alice.toml` configuration file. Add an `[account]` section as follow. 

```toml
[account]
unlock = ["0x12db1ee91481573302f63ebf3d735820081c68a2"]      # Alice's account to sign transactions from Remix.
password = ["alice.pwd"]
```
To make sure the file is valid, launch `Alice`'s node using the command `openethereum --config ./alice.toml` and verify that you do not get any error in the console. 
Also, copy in a text file the Public node address as we will use is later on. It should look similar to:
```bash
Public node URL: enode:////20d155e5b7279055257ae20d6c0e1a3935dc38cbc941fc5039d1b889e298ce34b5e9f082c84d3f1394984b341553a9ae426ce4372f33efc83b98afa50f51c3aa@172.18.0.1
```

### 4. Add bootnodes to each configuration file

We now have copied all the public URLs (enodes) of our nodes. We will use them to connect the nodes between each other so that we do not need to wait for them to discover themselves organically.

Uncomment and add the list of nodes to `Node0.toml`, `Ǹode1.toml` and `Alice.toml` so that each file contains lines similar to:
```toml
bootnodes = [
"enode://dd87dbdb2ce7eec42cc4655a3e5abe12cdd0ede81b44f5492c84c892a9b209d91f8f0fb12fca44a5206711ae91d68d6bd643a1a018e3f59de30cd0415691b91e@172.18.0.1:30300", # Node0
"enode://70f8784653cb189b6e2daf925f25f1ca3dfad266370b6ddd258551e48159bd5b9c23bdd39367155136a0ee7d6331d4107c0a765850ccc706c37686baefb039a1@172.18.0.1:30301", # Node1
"enode://20d155e5b7279055257ae20d6c0e1a3935dc38cbc941fc5039d1b889e298ce34b5e9f082c84d3f1394984b341553a9ae426ce4372f33efc83b98afa50f51c3aa@172.18.0.1:30303"  # Alice
]
```

To make sure that each node sees the others, launch the nodes simultaneously in different terminal windows and verify that they are each connected to 2 peers and importing the blocks created by `Node0`.


Find [here](https://github.com/Tbaut/Validator-set-tutorial/tree/master/Part-1-files) example files that you can compare yours with.

|[ ← Validator Set Tutorial Overview ](Validator-Set-Tutorial-Overview)| [ Part 2 - Deploy the Validator Set contract → ](Validator-Set-Tutorial-2)|
