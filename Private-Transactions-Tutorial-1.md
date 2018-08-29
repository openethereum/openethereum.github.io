---
title: Part 1 - Configuring each node
---

The Secret Store nodes have been configured in the [Secret Store tutorial](https://wiki.parity.io/Secret-Store-Tutorial-overview) and do not need to be modified for now.
The Secret Store is a crucial element of the private transactions feature. The Secret Store nodes manage the permissioning as well as the encryption/decryption key delivery for the private transactions and private contract.
Unlike the Secret Store tutorial, we will setup two distinct nodes for Alice and Bob as they will have different roles.
Alice's address will be used as the external account to sign and interact with the private contract, while Bob will be a validator.

### 0. Prerequisite

It is required to compile the `master` branch of parity-ethereum as bug fixes have been merged recently and are not present in any release branch yet. Please follow the steps to [build parity](https://wiki.parity.io/Setup#building-from-source)
```bash
# in parity-ethereum directory
git checkout master
cargo build --release --features secretstore
```

Parity's binary should then be available at `/path/to/parity-ethereum/target/release/parity`. This binary must be used every time the command `parity` appears in the tutorial.

### 1. Configure Alice's node
We will create a config file named `alice.toml` based on `users.toml` from the [Secret Store Tutorial](https://wiki.parity.io/Secret-Store-Tutorial-overview).
Alice's node needs its dedicated data directory. We also need to add the relevant JSON-RPC APIs to Alice's node: `["secretstore","eth","net","private","parity","personal"]`.

We will add a `[private_tx]` section where we need to specify the parameters related to our private transaction system. Alice's account will be used to sign the public transactions (to deploy the public contract containing the private contract for instance). This is specified using the field `signer`. This is the only entry specific to Alice's node. We will then specify the url to reach the Secret Store as well as the account that should be used to sign messages to the Secret Store. 

The list of bootnodes correspond to the Secret Store nodes for now; we will add Alice and Bob's node later on.

Here is how the file `alice.toml` should look likes:

```toml
# Alice's config file in alice.toml

[parity]
chain = "dev"
base_path = "db.alice" #use a dedicated data directory

[rpc]
port = 8545 #default http port for RPC
apis = ["secretstore","eth","net","private","parity","personal"]    #add "private","parity","personal"
cors = ["http://remix.ethereum.org"]                                # allow remix to access this node

[secretstore]
disable = true # users do not run a secret store node

[private_tx]
enabled = true                                              # Enable private transactions.
signer = "0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046"       # Alice's account to sign public transactions created upon verified private transaction.
account = "0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046"      # Alice's account to sign requests sent to the Secret Store.
passwords = "alice.pwd"                                     # File containing the password to unlock Alice accounts (signer, private account, validators).
sstore_url = "http://127.0.0.1:8010"                        # Specify secret store URL used for encrypting private transactions.

[network]
port = 30300
bootnodes = [
  "enode://59ff7f71a8ced85f23d2455b8645931e962886cce7025fd4fb769c4881c505d8445aa24be98b1aa3067cf7490a2ff0cd1558c37f6a536a4d799f8d93c3fe21ea@127.0.0.1:30301",
  "enode://800cf3b097974b9740d9d7aeda28bf1655e7c30a10bdb5f616ee0b41b786c13ce8d4008854d96430193b7cb4710a59c418566d5f6111bce4a18319757eaec358@127.0.0.1:30302",
  "enode://58815b57d8af2bc04963bde42b27deca674c18dca4098b8891296479ce0a83c2398a141babb835f181c6447bb1ac2ce4dca88ec20908d41b86166018d842fab4@127.0.0.1:30303",
]

```

### 2. Configure Bob's node

Bob's configuration file will specify Bob's account can be used to validate private transactions. No need for RPC server here.
We will create a config file named `bob.toml`, using a dedicated directory for Bob and a network port that isn't used yet.
We also need to add a `[private_tx]` section. As Bob's node will not be used to sign public transactions, the `signer` entry is not needed. Other than that, the entries are the same as Alice's node.

Bob's toml
```toml
# Users config file in bob.toml

[parity]
chain = "dev"
base_path = "db.bob" #use a dedicated data directory

[rpc]
disable = true

[websockets]
disable = true

[secretstore]
disable = true # users do not run a secret store node

[private_tx]
enabled = true                                                # Enable private transactions.
validators = ["0xfeacd0d28fd158ba2d3adb6d69d20c723214edc9"]   # Bob's account as a validator
account = "0xfeacd0d28fd158ba2d3adb6d69d20c723214edc9"        # Bob's address to signing requests sent to the Secret Store.
passwords = "bob.pwd"                                         # File containing the password to unlock Bob's accounts (signer, private account, validators).
sstore_url = "http://127.0.0.1:8010"                          # Secret Store URL used for encrypting private transactions.

[network]
port = 30304  #use a dedicated networking port
bootnodes = [
  "enode://59ff7f71a8ced85f23d2455b8645931e962886cce7025fd4fb769c4881c505d8445aa24be98b1aa3067cf7490a2ff0cd1558c37f6a536a4d799f8d93c3fe21ea@127.0.0.1:30301",
  "enode://800cf3b097974b9740d9d7aeda28bf1655e7c30a10bdb5f616ee0b41b786c13ce8d4008854d96430193b7cb4710a59c418566d5f6111bce4a18319757eaec358@127.0.0.1:30302",
  "enode://58815b57d8af2bc04963bde42b27deca674c18dca4098b8891296479ce0a83c2398a141babb835f181c6447bb1ac2ce4dca88ec20908d41b86166018d842fab4@127.0.0.1:30303",
]
```

Note that the `validators` field is an array. A node could use several identities to validate several private contracts. In the next steps of this tutorial, we will deploy a private contract specifying Bob's account as validator. Because of this, there needs to be a node on the network running with Bob's account in the `validators` field and the associated password in the `passwords` field. Otherwise, no private transaction can be deployed. In our case, the node we've just setup will validate the private transactions.

## 3. Finalize configurations
 
### 3.1 Collect enodes's addresses
 
To make sure that Alice and Bob's nodes are connected to each other, we should specify them as bootnodes. To do so, we first need to know their enode address by launching Parity Ethereum. Parity Ethereum will also create the base directories that we specified (`db.alice` and `db.bob`).  

Launch Parity Ethereum with `parity --config alice.toml` and copy the enode address printed in the console. Do the same for Bob with `parity --config bob.toml`.
For instance:  
- Alice: `enode://29ebcddebe4d2c13632080e12a39ef89ff245893e3a7685676a01cbea011395bfce72e82664a9379eb5f3916e9e4f0df5de65d2559e87c28ceb291d53aee6fe0@127.0.0.1:30300`
- Bob: `enode://00db9262e364d683852488c0b404b146268bc1be3df01bbd572e33692d4a8dbd4b8b6ab300e70a01cb3a5024fc164fc355041afdfe82642f3e36c61763b0f22d@127.0.0.1:30304`

Note that you should replace the IP at the end to use localhost `127.0.0.1`.  
We now have two new directories (`db.alice` and `db.bob`) where we will copy the keys from Alice and Bob created in the Secret Store tutorial.

### 3.2 Manage Alice and Bob's keys

If you wish to use the same users and keys as the Secret Store tutorial, go ahead and copy Alice and Bob's keys from the `db.users/keys/DevelopmentChain/` directory into `db.alice/keys/DevelopmentChain/` and `db.bob/keys/DevelopmentChain/` respectively. Using the following command for instance:

```bash
cp Secret_store_tutorial_directory/db.users/keys/DevelopmentChain/file-for-alice-key Private_tx_tutorial_directory/db.alice/keys/DevelopmentChain/file-for-alice-key

```

To know which file corresponds to which key, open them with a text editor and read the addresses.

You should also create password files for Alice and Bob, `alice.pwd` and `bob.pwd` with `alicepwd` and `bobpwd` respectively. You can do so using the command:
```bash

echo alicepwd > alice.pwd
echo bobpwd > bob.pwd
```

### 3.3 Update Alice and Bob's configuration files

You can now edit Alice and Bob's configuration files to add the bootnodes to each file:

Both configuration files should now have the following bootnodes:
```toml
bootnodes = [
  "enode://59ff7f71a8ced85f23d2455b8645931e962886cce7025fd4fb769c4881c505d8445aa24be98b1aa3067cf7490a2ff0cd1558c37f6a536a4d799f8d93c3fe21ea@127.0.0.1:30301",
  "enode://800cf3b097974b9740d9d7aeda28bf1655e7c30a10bdb5f616ee0b41b786c13ce8d4008854d96430193b7cb4710a59c418566d5f6111bce4a18319757eaec358@127.0.0.1:30302",
  "enode://58815b57d8af2bc04963bde42b27deca674c18dca4098b8891296479ce0a83c2398a141babb835f181c6447bb1ac2ce4dca88ec20908d41b86166018d842fab4@127.0.0.1:30303",
  "enode://29ebcddebe4d2c13632080e12a39ef89ff245893e3a7685676a01cbea011395bfce72e82664a9379eb5f3916e9e4f0df5de65d2559e87c28ceb291d53aee6fe0@127.0.0.1:30300",
  "enode://00db9262e364d683852488c0b404b146268bc1be3df01bbd572e33692d4a8dbd4b8b6ab300e70a01cb3a5024fc164fc355041afdfe82642f3e36c61763b0f22d@127.0.0.1:30304"
]
```

Add the following to `alice.toml` to be able to deploy a contract using Remix:
```toml
[account] # unlock Alice's account to deploy the contract
unlock = ["0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046"]
password = ["alice.pwd"]
```

You can find the complete set of files in [this repository](https://github.com/Tbaut/Private-Transations-Tutorial-files/tree/master/config).

The node's configuration is now complete, you can launch Alice, Bob's and the Secret Store nodes with their respective toml file.

|[ ← Tutorial overview ](Private-Transactions-Tutorial-Overview.md) | [ Part 2 - Permissioning contract → ](Private-Transactions-Tutorial-2.md)|

