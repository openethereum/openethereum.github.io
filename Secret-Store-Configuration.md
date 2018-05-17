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

The main disadvantage of hardcoded list is that if you need to change nodes set, you have to stop all nodes, change their configuration files and finally restart. Another option is to use blockchain contract to configure the set (see [Nodes set contracts](#nodes-set-contracts)), by using following option in the configuration file:
```toml
[secretstore]
server_set_contract = "registry"
```

There are three possible values for this option:
- `"none"` - nodes set will be read from the `nodes` configuration option;
- `"registry"` - nodes set contract address will be read from `server_set_contract` entry in the Registry;
- `address` - this node will be configured for the nodes set contract, residing at given address.

### Configuring Secret Store HTTP API
Secret Store HTTP API is enabled by default on `local` interface and port `8082`. To change these settings, use following configuration options:
```toml
[secretstore]
disable_http = false
http_interface = "local" # local, all or address
http_port = 12001
```

You can disable HTTP API by setting `disable_http` option to `true`.

### Configuring Secret Store service contract API
As an alternative way to provide Secret Store API, you can deploy a set of service contracts on the blockchain and Secret Store will serve requests, coming to these contracts. There are currently four configuration options for these contracts:
```toml
[secretstore]
service_contract_srv_gen = "50D59af572FF790D665C339E60128C511c2656f0"
service_contract_srv_retr = "registry"
service_contract_doc_store = "none"
service_contract_doc_sretr = "registry"
```

Every service contract is responsible for providing access to exactly one of the session types:
`service_contract_srv_gen` - allows its clients to start [Server key generation session](Secret-Store.md#server-key-generation-session);
`service_contract_srv_retr` - allows its clients to start Server key retrieval session;
`service_contract_doc_store` - allows its clients to start [Document key storing session](Secret-Store.md#document-key-storing-session);
`service_contract_doc_sretr` - allows its clients to start [Document key shadow retrieval session](Secret-Store.md#document-key-shadow-retrieval-session).

Every option can have following values:
- `"none"` - this node won't serve requests coming from this kind of a contract;
- `"registry"` - contract address will be read from registry (entries are named: `secretstore_service_srv_gen`, `secretstore_service_srv_retr`, `secretstore_service_doc_store` and `secretstore_service_doc_sretr` accordingly);
- `address` - this node will listen to requests from the contract, residing at given address.

For more information on how every contract works, please refer to [Secret Store service contracts](#secret-store-service-contracts)

### Configuring Secret Store permissioning contract
There's an option to configure address of the permissioning contract in Secret Store:
```toml
[secretstore]
acl_contract = "registry"
```

This option can have 3 values:
- `"none"` - node will be configured to work without actual permission checks. This means that it will hand out its keys shares to anyone who will request it. **This mode is intended for tests and must NOT be used in production**;
- `"registry"` - permissioning contract address will be read from `secretstore_acl_checker` entry in the Registry;;
- `address` - permissioning contract resides at given address.

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

One last note about this session: the key can be lost forever, if number of nodes falls below `t+1` as a result of this session. So avoid situations when there are a lot of isolated nodes or there's a lot of nodes being removed without replacement. *And choose key threshold wisely*.

## Nodes set contracts
There's a way to read list of other nodes from the blockchain contract, instead of configuration file. There are two types of such contracts: with and without auto-migration support. Both contracts are supporting at most 256 key servers.

### Nodes set contracts without auto-migration support
This type of contract is suitable for cases, when you do not bother with running [nodes set change session](#changing-servers-set-configuration), or you're going to run it manually. The contract must implement the following interface:
```sol
/// Simple key server set.
interface KeyServerSet {
	/// Get number of block when current set has been changed last time.
	function getCurrentLastChange() external view returns (uint256);
	/// Get index of given key server in current set.
	function getCurrentKeyServerIndex(address keyServer) external view returns (uint8);
	/// Get count of key servers in current set.
	function getCurrentKeyServersCount() external view returns (uint8);
	/// Get address of key server in current set.
	function getCurrentKeyServer(uint8 index) external view returns (address);

	/// Get all current key servers.
	function getCurrentKeyServers() external view returns (address[]);
	/// Get current key server's public key.
	function getCurrentKeyServerPublic(address keyServer) external view returns (bytes);
	/// Get current key server's address.
	function getCurrentKeyServerAddress(address keyServer) external view returns (string);
}
```

The `disable_auto_migrate` must be set to `true` in configuration file, if you're using this type of the contract.

### Nodes set contracts with auto-migration support
This type of contract is used when you want to auto-start [nodes set change session](#changing-servers-set-configuration) (auto migration), when nodes set changes. The contract must implement the following interface:
```sol
/// Key server set with migration support.
interface KeyServerSetWithMigration {
	/// When new server is added to new set.
	event KeyServerAdded(address keyServer);
	/// When existing server is removed from new set.
	event KeyServerRemoved(address keyServer);
	/// When migration is started.
	event MigrationStarted();
	/// When migration is completed.
	event MigrationCompleted();

	/// Get number of block when current set has been changed last time.
	function getCurrentLastChange() external view returns (uint256);
	/// Get index of given key server in current set.
	function getCurrentKeyServerIndex(address keyServer) external view returns (uint8);
	/// Get count of key servers in current set.
	function getCurrentKeyServersCount() external view returns (uint8);
	/// Get address of key server in current set.
	function getCurrentKeyServer(uint8 index) external view returns (address);

	/// Get all current key servers.
	function getCurrentKeyServers() external view returns (address[]);
	/// Get current key server's public key.
	function getCurrentKeyServerPublic(address keyServer) external view returns (bytes);
	/// Get current key server's address.
	function getCurrentKeyServerAddress(address keyServer) external view returns (string);

	/// Get all migration key servers.
	function getMigrationKeyServers() external view returns (address[]);
	/// Get migration key server's public key.
	function getMigrationKeyServerPublic(address keyServer) external view returns (bytes);
	/// Get migration key server's address.
	function getMigrationKeyServerAddress(address keyServer) external view returns (string);

	/// Get all new key servers.
	function getNewKeyServers() external view returns (address[]);
	/// Get new key server's public key.
	function getNewKeyServerPublic(address keyServer) external view returns (bytes);
	/// Get new key server's address.
	function getNewKeyServerAddress(address keyServer) external view returns (string);

	/// Get migration id.
	function getMigrationId() external view returns (bytes32);
	/// Get migration master.
	function getMigrationMaster() external view returns (address);
	/// Is migration confirmed by given node?
	function isMigrationConfirmed(address keyServer) external view returns (bool);
	/// Start migration.
	function startMigration(bytes32 id) external;
	/// Confirm migration.
	function confirmMigration(bytes32 id) external;
}
```

For an example implementation of this type of contract, please refer to [SetOwnedWithMigration.sol](https://github.com/parity-contracts/secretstore-key-server-set/blob/cf2d4cb4becf7a71686b2d66fa34ac4c5a46d610/contracts/SetOwnedWithMigration.sol). The `disable_auto_migrate` must be set to `false` in configuration file, if you're using this type of the contract.

There are 3 sets of servers in this contract: current (this is a set, which is currently responsible for serving SS requests), new (all changes should be made here) and migration (not empty during migration process). Auto migration starts when one of key servers from current set sees that there are changes in new set, when comparing to current set. Migration starts when `startMigration` method is called. After migration is started, key servers are running [nodes set change session](#changing-servers-set-configuration). When it completes, every key server from migration set confirms that migration has completed by calling the `confirmMigration` method.

Please notice, that nodes are sending transactions to this contract. So make sure that every node (from 'current', 'new' and 'migration' sets) has enough ETH to cover the gas costs. Otherwise, migration can never start or never complete.

## Secret Store service contracts
There are two kind of participants, sending transactions to service contracts:
- SS clients, wanting to access SS API;
- SS nodes, responding to service contracts requests.

This implies different contract interfaces for different participant types - one is 'ClientApi' and the other is 'KeyServerApi'. Key servers are sending transactions to service contracts, so please make sure that key servers accounts has enough ether to cover gas costs. [Reference implementation]
(https://github.com/parity-contracts/secretstore-service/pull/1/files) of service contracts requires a fee to be paid for every request. The fee is splitted among all key servers and can be transferred to their accounts by calling 'drain' method. This can be used as a source of ETH to pay transactions costs.

Here we will consider 'ClientApi' interfaces only. One common thing about these interfaces is that they all have single method to register request and one or two events, signalling that request has been served. There's always an additional event, signalling that request has failed. It doesn't log the real reason behind the failure, though - you can only find it in nodes logs.

Another important notice is that [reference implementation](https://github.com/parity-contracts/secretstore-service/pull/1/files) reads the list of key servers from [nodes set contract](#nodes-set-contracts). So you'll be unable to use service contracts when nodes set is configured in the configuration file.

### Server Key generation service contract
The client API is straightforward - just call the `generateServerKey` method and wait until key is either generated (`ServerKeyGenerated`), or an generation error occurs (`ServerKeyGenerationError`):
```sol
/// Server Key generation service contract API (client view).
interface ServerKeyGenerationServiceClientApi {
	/// When server key is generated.
	event ServerKeyGenerated(bytes32 indexed serverKeyId, bytes serverKeyPublic);
	/// When error occurs during server key generation.
	event ServerKeyGenerationError(bytes32 indexed serverKeyId);

	/// Request new server key generation. Generated key will be published via ServerKeyGenerated event when available.
	function generateServerKey(bytes32 serverKeyId, uint8 threshold) external payable;
}
```

### Server Key retrieval service contract
The client API is straightforward - just call the `retrieveServerKey` method and wait until key is either retrieved (`ServerKeyRetrieved`), or an generation error occurs (`ServerKeyRetrievalError`):
```sol
/// Server Key retrieval service contract API (client view).
interface ServerKeyRetrievalServiceClientApi {
	/// When server key is retrieved.
	event ServerKeyRetrieved(bytes32 indexed serverKeyId, bytes serverKeyPublic, uint256 threshold);
	/// When error occurs during server key retrieval.
	event ServerKeyRetrievalError(bytes32 indexed serverKeyId);

	/// Retrieve existing server key. Retrieved key will be published via ServerKeyRetrieved or ServerKeyRetrievalError.
	function retrieveServerKey(bytes32 serverKeyId) external payable;
}
```

### Document Key store service contract
The client API is straightforward - just call the `storeDocumentKey` method and wait until key is either stored (`DocumentKeyStored`), or storing error occurs (`DocumentKeyStoreError`):
```sol
/// Document Key store service contract API (client view).
interface DocumentKeyStoreServiceClientApi {
	/// When document key is stored.
	event DocumentKeyStored(bytes32 indexed serverKeyId);
	/// When error occurs during document key store.
	event DocumentKeyStoreError(bytes32 indexed serverKeyId);

	/// Request document key store. Use `secretstore_generateDocumentKey` RPC to generate both
	/// `commonPoint` and `encryptedPoint`.
	function storeDocumentKey(bytes32 serverKeyId, bytes commonPoint, bytes encryptedPoint) external payable;
}
```

### Document Key shadow retrieval service contract
In this client API, request is registered by calling the `retrieveDocumentKeyShadow` method. Retrieval consists of two stages: at first, 'common' data is retrieved and published via `DocumentKeyCommonRetrieved` event. Common data includes `commonPoint` and `threshold`. After common data is retrieved, the client must listen for `DocumentKeyPersonalRetrieved` events. Once there are exactly `threshold+1` events with **the same** `decryptedSecret` value, you could pass `commonPoint`, `decryptedSecret` and all `shadows` from gathered `DocumentKeyPersonalRetrieved` events to `secretstore_shadowDecrypt` RPC method to decrypt encrypted data.
```sol
/// Document Key shadow retrieval service contract API (client view).
interface DocumentKeyShadowRetrievalServiceClientApi {
	/// When document key common portion is retrieved. Ater this event s fired, wait for
	/// exactly `threshold+1` `DocumentKeyPersonalRetrieved` events with the same `decryptedSecret` value.
	event DocumentKeyCommonRetrieved(bytes32 indexed serverKeyId, address indexed requester, bytes commonPoint, uint8 threshold);
	/// When document key personal portion is retrieved. After enough events are fired, use `secretstore_shadowDecrypt`
	/// to decrypt document contents.
	event DocumentKeyPersonalRetrieved(bytes32 indexed serverKeyId, address indexed requester, bytes decryptedSecret, bytes shadow);
	/// When error occurs during document key retrieval.
	event DocumentKeyShadowRetrievalError(bytes32 indexed serverKeyId, address indexed requester);

	/// Request document key retrieval.
	function retrieveDocumentKeyShadow(bytes32 serverKeyId, bytes requesterPublic) external payable;
}
```

### ECDSA signature generation contract
*In development*

### Service contracts on Kovan
All service contracts, described above, are deployed on the Kovan testnet (**testnet means that it should be used for tests only - please do not rely on this as an infrastructure for your non-test applications**). This gives you an ability to store encryption keys even without deploying your own Secret Store. Here's the information required to use these contracts:
- latest deployment information (including service contract addresses) can be found in [secretstore-service](https://github.com/parity-contracts/secretstore-service/blob/165b6083c5f29d8baa01bffad5e0a7d7b365dae0/README.md) repository;
- there are currently 5 key servers deployed (so maximum `threshold` is 4);
- all deployed key servers are configured to use [OnceTransferablePermission.sol](https://github.com/parity-contracts/secretstore-acl/blob/17dd8f3f97f601723b3e13f11d515e4ebb457b95/contracts/OnceTransferablePermission.sol) permissioning contract (it is a contract with artificially limited abilities to use in tests only), with deployment data published [here](https://github.com/parity-contracts/secretstore-acl/blob/17dd8f3f97f601723b3e13f11d515e4ebb457b95/README.md). So, before generating key with new ID, please make sure that you have 'reserved' (called `createKey`) the key id for your own account;
- processing requests could take some time (up to 10 blocks for [Document Key shadow retrieval](#document-key-shadow-retrieval-service-contract) requests and 4-5 blocks for all other requests). It could be even more, when key server misses the request (because of synchronization). The general rule - if your request haven't processed for >100 blocks, then something is definitely wrong;
- you are not the only user of this Secret Store, so expect keys with IDs like '0x0000000000000000000000000000000000000000000000000000000000000001' to be occupied and some requests to fail because of this.

If you experience any problems with service contracts on Kovan, please report at [Parity Support](https://riot.im/app/#/room/#support:matrix.parity.io).