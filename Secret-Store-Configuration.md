---
title: Secret Store Configuration
---

## Building Parity with Secret Store support
By default Parity is built without Secret Store support. To enable Secret Store, build it with `--features secretstore` argument:
```bash
git clone https://github.com/paritytech/parity.git
cd parity
cargo build --features secretstore --release
```

## Configuring nodes
Every Secret Store node must be properly configured. The best way is to create [configuration files](Configuring-Parity.md#config-file). All Secret Store specific configuration options must be placed in `[secretstore]` section of this file.

### Configuring key pairs
Secret Store is a closed nodes group - i.e. nodes are connecting and accepting connections to/from known nodes only. This means that address of each node is known to the whole Secret Store. To prove that it is the correct node who is listening on this address, key pair must be assigned to every node.

The best option to specify key pair is to use account, created in Parity. It can be created either manually, or by using `ethstore` utility. You'll also need file with this password' account. After creating an account, add following lines to your configuration file:
```toml
[account]
password = ["password.file"] # name of file with account' password

[secretstore]
self_secret = "6fe74e132437594baba2454a3c7132050154b0c8" # address of the account
```

Another option is to create key pair manually:
```bash
openssl ecparam -name secp256k1 -genkey -noout | openssl ec -text -noout > keyserver.key 2>nul
printf "PRIVATE KEY: 0x"
cat keyserver.key | grep priv -A 3 | tail -n +2 | tr -d '\n[:space:]:' | sed 's/^00//'
printf "\n\rPUBLIC KEY: 0x"
cat keyserver.key | grep pub -A 5 | tail -n +2 | tr -d '\n[:space:]:' | sed 's/^04//'
printf "\n\r"
```

and specify private portion in the `self_secret` configuration option:
```toml
[secretstore]
self_secret = "30c11fa62e828a2f7d0e12ef0429dc2b5f79de4c89847d328c8418e618d8d5bb" # private key
```

### Configuring connections
You should specify address to listen to for incoming connections:
```toml
[secretstore]
interface = "local" # local, all or address
port = 12000
```

There are two ways to specify Secret Store nodes set. The easiest option is to hardcode addresses + public key of other nodes in the configuration file:
```toml
[secretstore]
nodes = ["165872fc7999f12f8d5a4156d8cf95cdd363ab2dffb53effcef926863beead042e926df1c54e341708f9544668478dadb81467b3aae0c1afda57ad1c9aad7935@127.0.0.1:12001", "14326ed4da50bc7e9760a6ba0e5246fb850909a03078fe44036d09e6edf6d0bd782eff5c2def79ba5b7025daec9c79166585ad6a99353f79a728cdda94ab8929@127.0.0.1:12002", "62844fcd0c213a3bd4a7bcb67736725573bca9e09cf7777d83fd57f11cc7f7b43444dfced2868e3abdef1758dab56da5998e21f013aab2355e10d03ab7cd60c6@127.0.0.1:12003"]
```
Format of node connection is `public_key_of_the_node@node_address`.

The main disadvantage of hardcoded list is that if you need to change nodes set, you have to stop all nodes, change their configuration files and finally restart. Another option is to use blockchain contract to configure nodes set (see [Nodes set contracts](#nodes-set-contracts)).

### Configuring Secret Store API
Secret Store HTTP API is enabled by default on `local` interface and port `8082`. To change these settings, use following configuration options:
```toml
[secretstore]
disable_http = false
http_interface = "local" # local, all or address
http_port = 12001
```

You can disable HTTP API by setting `disable_http` option to `true`.

The same API can be provided using blockchain contract (see [Secret Store API contract](#secret-store-api-contract)).

### Example configuration file
Examples of different configuration files and scripts for their generation can be found in [Secret Store tests](https://github.com/svyatonik/sstore_test) repository. Here's single configuration file example:
```toml
[secretstore]
disable = false # set to true to disable Secret Store functionality completely
self_secret = "30c11fa62e828a2f7d0e12ef0429dc2b5f79de4c89847d328c8418e618d8d5bb"
nodes = ["165872fc7999f12f8d5a4156d8cf95cdd363ab2dffb53effcef926863beead042e926df1c54e341708f9544668478dadb81467b3aae0c1afda57ad1c9aad7935@127.0.0.1:12001"]
interface = "local"
port = 12000
http_interface = "local"
http_port = 8082
path = "db.poa_ss1/secretstore" # path to Secret Store keys storage
```

## Changing servers set configuration
If you want to include or exclude node to/from Secret Store, you must take into account two factors:
1) if you're excluding node, it can happen that after exclusion some secrets became irrecoverable (there will be less secret shares in Secret Store than it is required to recover the secret);
2) if you're adding node, you might want to add additional shares to existing secrets to avoid situation from (1) in the future.

That's where nodes set change session helps. The idea of this session is to **securely** add shares for all keys on nodes being added to Secret Store and wipe database on nodes being removed. *Securely* here means that server/document key is not restored on any node. The implementation of this session is based on ["Verifiable Secret Redistribution for Threshold Sharing Schemes"](http://www.cs.cmu.edu/~wing/publications/CMU-CS-02-114.pdf) paper.

This session is so called 'administrative' session, which can only be started by Secret Store 'administrator'. Administrator owns the key, which public portion should be known to all nodes. It is configured by adding following option to configuration file:
```toml
[secretstore]
admin_public = "002cad13cf0aeb7f5f7c4cda5dd677902c24f2167e0edefdcc4434286825746cf8d46d705b150b006c3a96c905cd9d1a88e4ed61b99212836011c627959fb431"
```

There are 4 kind of nodes in nodes set change session:
- added nodes: nodes that are being added to Secret Store;
- removed nodes: nodes that are being removed from Secret Store;
- stable nodes: nodes that are staying in Secret Store.
- isolated nodes: nodes which are already not a part of Secret Store.
Ideally, there are only added, removed and stable nodes, but eventually there'll be nodes, which have been forcibly removed from Secret Store because of failure or misbehavior (isolated nodes).

Nodes set change session requires that all 'added', 'removed' and 'stable' nodes must be online for this session duration. Before staring session, you'll need to generate two administrator' signatures:
- 'old set' signature: ECDSA signature of all online nodes ids `keccak(ordered_list(staying + added + removing))`;
- 'new set' signature: ECDSA signature of nodes ids, that should stay in Secret Store after this session `keccak(ordered_list(staying + added))`.
To generate these signatures, you could use two Secret Store RPC methods - [`secretstore_serversSetHash`](JSONRPC-secretstore-module.md#secretstore_serverssethash) and [`secretstore_signRawHash`](JSONRPC-secretstore-module.md#secretstore_signrawhash). To start session, use following command:
```bash
curl --data-binary '["0xa7cc7a8ef336189c2bfabfeab8eed55598fa2b480adf98eeb66e006f2811319550222122bd37fb25dbc35709ccd5d9793dc829d208b73ffbce893d63a393101b", "0x54319671ca191b9e08e2064d8b9eaa43cb246e698dc7d995d557ebef3428dc69b93ca3caa20c43552414132448425c9333aa33b2231caa082ddcec7e12a56963"]' -X POST http://localhost:8084/admin/servers_set_change/17374292b9e1a026e1e87fe37ae4e987156d1979461b6daa8ec804ed667e8d100d8129295172a86db63c12b1aa6bb861e89b3ea4330e4b8ae27e0e1917485c1e01/ff5fbcfa6c05d6a1353023e5ade51c3ab53bead7466c1bacd510ee3ef93814ef2a3340cdd7991bea362aac9f111795a470b70d5f4e9ad9877338089c9aacbdb200
```

Here:
- `["0xa7cc7a8ef336189c2bfabfeab8eed55598fa2b480adf98eeb66e006f2811319550222122bd37fb25dbc35709ccd5d9793dc829d208b73ffbce893d63a393101b", "0x54319671ca191b9e08e2064d8b9eaa43cb246e698dc7d995d557ebef3428dc69b93ca3caa20c43552414132448425c9333aa33b2231caa082ddcec7e12a56963"]` - request body, containing set of 'new set' nodes ids;
- `17374292b9e1a026e1e87fe37ae4e987156d1979461b6daa8ec804ed667e8d100d8129295172a86db63c12b1aa6bb861e89b3ea4330e4b8ae27e0e1917485c1e01` - 'old set' signature;
- `ff5fbcfa6c05d6a1353023e5ade51c3ab53bead7466c1bacd510ee3ef93814ef2a3340cdd7991bea362aac9f111795a470b70d5f4e9ad9877338089c9aacbdb200` - 'new set' signature.

That said, here's the actions required to start nodes set change session:
1) select nodes sets and prepare sets signatures;
2) stop all running nodes;
3) change configuration files so that nodes from both 'old set' and 'new set' are fully connected;
4) start all nodes from 'old set' and 'new set';
5) start nodes set change session and wait for its completion;
6) stop all running nodes;
7) change configuration files so that only nodes from 'new set' are fully connected;
8) start all nodes from 'new set'.

One last note about this session: it will fail if there is at least one server key, which is irrecoverable or which will became irrecoverable after nodes set change session. So avoid situations when there are a lot of isolated nodes or there's a lot of nodes being removed without replacement. *And choose key threshold wisely*.

## Nodes set contracts
There's a way to read list of other nodes from the blockchain contract, instead of configuration file. There are two types of such contracts: with and without auto-migration support. Both types of contract must be registered in the Registry under `secretstore_server_set` name.

### Nodes set contracts without auto-migration support
This type of contract is suitable for cases, when you do not bother with running [nodes set change session](#changing-servers-set-configuration), or you're running it manually. The contract must implement the following interface:
```sol
// Simple key server set.
interface KeyServerSet {
	// Get all current key servers.
	function getCurrentKeyServers() public constant returns (address[]);
	// Get current key server public key.
	function getCurrentKeyServerPublic(address keyServer) public constant returns (bytes);
	// Get current key server address.
	function getCurrentKeyServerAddress(address keyServer) public constant returns (string);
}
```

The `disable_auto_migrate` must be set to `true` in configuration file, if you're using this type of the contract.

### Nodes set contracts with auto-migration support
This type of contract is used when you want to auto-start [nodes set change session](#changing-servers-set-configuration) (auto migration), when nodes set changes. The contract must implement the following interface:
```sol
// Key server set with migration support.
interface KeyServerSetWithMigration {
	// When new server is added to new set.
	event KeyServerAdded(address keyServer);
	// When existing server is removed from new set.
	event KeyServerRemoved(address keyServer);
	// When migration is started.
	event MigrationStarted();
	// When migration is completed.
	event MigrationCompleted();

	// Get all current key servers.
	function getCurrentKeyServers() public constant returns (address[]);
	// Get current key server public key.
	function getCurrentKeyServerPublic(address keyServer) public constant returns (bytes);
	// Get current key server address.
	function getCurrentKeyServerAddress(address keyServer) public constant returns (string);

	// Get all migration key servers.
	function getMigrationKeyServers() public constant returns (address[]);
	// Get migration key server public key.
	function getMigrationKeyServerPublic(address keyServer) public constant returns (bytes);
	// Get migration key server address.
	function getMigrationKeyServerAddress(address keyServer) public constant returns (string);

	// Get all new key servers.
	function getNewKeyServers() public constant returns (address[]);
	// Get new key server public key.
	function getNewKeyServerPublic(address keyServer) public constant returns (bytes);
	// Get new key server address.
	function getNewKeyServerAddress(address keyServer) public constant returns (string);

	// Get migration id.
	function getMigrationId() public view returns (bytes32);
	// Get migration master.
	function getMigrationMaster() public constant returns (address);
	// Is migration confirmed by given node?
	function isMigrationConfirmed(address keyServer) public view returns (bool);
	// Start migration.
	function startMigration(bytes32 id) public;
	// Confirm migration.
	function confirmMigration(bytes32 id) public;
}
```

For example implementation, see [SetOwnedWithMigration.sol](https://github.com/svyatonik/contracts/blob/94891dbacf2be8edc497396e41add21117b63359/contracts/SetOwnedWithMigration.sol). The `disable_auto_migrate` must be set to `false` in configuration file, if you're using this type of the contract.

## Secret Store API contract
In addition to Secret Store HTTP API (or as its replacement), there could be a blockchain contract, which is used to provide the same Secret Store functionality. The contract and related functionality is currently under development and only one API method is available - [Server key generation session](Secret-Store.md#server-key-generation-session).

To try it, please deploy contract from [SecretStoreServiceFixed.sol](https://github.com/svyatonik/contracts/blob/94891dbacf2be8edc497396e41add21117b63359/SecretStoreServiceFixed.sol) and register it under `secretstore_service` name in the Registry. Add following configuration option to all nodes configuration files: `service_contract = "registry"`. Now you can try to call `generateServerKey` method on the contract and public portion of the server key will be published via `ServerKeyGenerated` event within few blocks.

Please note that for this contract to work, nodes keys must be the same in configuration file and in `SecretStoreService` constructor.
