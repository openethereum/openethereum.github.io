---
title: Secret Store Configuration
---

## Building OpenEthereum with Secret Store support
By default, OpenEthereum is built without Secret Store support. To enable Secret Store, build it with the `--features secretstore` argument:
```bash
git clone https://github.com/openethereum/openethereum
cd openethereum
cargo build --features secretstore --release
```

## Configuring nodes
Every Secret Store node must be properly configured. The best way is to create a [configuration file](Configuring-OpenEthereum#config-file). The Secret Store specific configuration options must be placed in the `[secretstore]` section of this file.

### Configuring key pairs
Secret Store is a closed group of nodes - i.e. the nodes are connecting and accepting connections to/from known nodes only. This means that the address of each node is known to the whole Secret Store. To prove that the expected node is listening to each address, key pairs are assigned to each node.

The best option to specify a key pair is to use an account created in OpenEthereum. It can be created either manually, or by using the `ethstore` utility. You'll also need a file with this account's password. After creating an account, add the following lines to your configuration file:
```toml
[account]
password = ["password.file"] # file containing the account's password

[secretstore]
self_secret = "6fe74e132437594baba2454a3c7132050154b0c8" # address of the account
```

Another option is to create a key pair manually:
```bash
openssl ecparam -name secp256k1 -genkey -noout | openssl ec -text -noout > keyserver.key 2>nul
printf "PRIVATE KEY: 0x"
cat keyserver.key | grep priv -A 3 | tail -n +2 | tr -d '\n[:space:]:' | sed 's/^00//'
printf "\n\rPUBLIC KEY: 0x"
cat keyserver.key | grep pub -A 5 | tail -n +2 | tr -d '\n[:space:]:' | sed 's/^04//'
printf "\n\r"
```

and specify the private key in the `self_secret` configuration option:
```toml
[secretstore]
self_secret = "30c11fa62e828a2f7d0e12ef0429dc2b5f79de4c89847d328c8418e618d8d5bb" # private key
```

### Configuring connections
You should specify the address to listen to for incoming connections:
```toml
[secretstore]
interface = "local" # local, all or a specific address
port = 12000
```

There are two ways to specify a set of Secret Store nodes. The easiest option is to hardcode the node's addresses and their respective public key in the configuration file:
```toml
[secretstore]
server_set_contract = "none"        # required option if you use hardcoded nodes set
nodes = [
    "165872fc7999f12f8d5a4156d8cf95cdd363ab2dffb53effcef926863beead042e926df1c54e341708f9544668478dadb81467b3aae0c1afda57ad1c9aad7935@127.0.0.1:12001",
    "14326ed4da50bc7e9760a6ba0e5246fb850909a03078fe44036d09e6edf6d0bd782eff5c2def79ba5b7025daec9c79166585ad6a99353f79a728cdda94ab8929@127.0.0.1:12002",
    "62844fcd0c213a3bd4a7bcb67736725573bca9e09cf7777d83fd57f11cc7f7b43444dfced2868e3abdef1758dab56da5998e21f013aab2355e10d03ab7cd60c6@127.0.0.1:12003"
]
```
The format of a node connection is `node_public_key@node_address`. The nodes set must be the same in all configuration files and have an entry for each node.

The main disadvantage of a hardcoded list is that in case of a change in the set of nodes, all the nodes must be stopped,  have their configuration file manually updated and restarted. Another option is to use a smart contract to configure the nodes (see [Node set contracts](#node-set-contracts)), by using the following option in the configuration file:
```toml
[secretstore]
server_set_contract = "registry"
```

There are three possible values for this option:
- `"none"` - the set of nodes will be read from the `nodes` configuration option;
- `"registry"` - the contract address for the set of nodes will be read from `server_set_contract` entry in the Registry;
- `address` - this node will be configured for the Node set contract, located at given address.

### Configuring Secret Store HTTP API
The Secret Store HTTP API is enabled by default on `local` interface and port `8082`. To change these settings, use the following configuration options:
```toml
[secretstore]
disable_http = false
http_interface = "local" # local, all or address
http_port = 12001
```

You can disable HTTP API by setting `disable_http` option to `true`.

### Configuring Secret Store service contract API
An alternative way to support the Secret Store API is to deploy a set of service contracts on the blockchain in order for the Secret Store to  serve the requests coming to these contracts. There are currently 4 configuration options for these contracts:
```toml
[secretstore]
service_contract_srv_gen = "50D59af572FF790D665C339E60128C511c2656f0"
service_contract_srv_retr = "registry"
service_contract_doc_store = "none"
service_contract_doc_sretr = "registry"
```

Every service contract provides access to one of the follwoing session types:
- `service_contract_srv_gen` - allows its clients to start [Server key generation session](Secret-Store#server-key-generation-session);
- `service_contract_srv_retr` - allows its clients to start Server key retrieval session;
- `service_contract_doc_store` - allows its clients to start [Document key storing session](Secret-Store#document-key-storing-session);
- `service_contract_doc_sretr` - allows its clients to start [Document key shadow retrieval session](Secret-Store#document-key-shadow-retrieval-session).

Each option can have one of the following values:
- `"none"` - this node won't serve requests coming from this kind of a contract;
- `"registry"` - the contract's address will be read from the registry (entries are named: `secretstore_service_srv_gen`, `secretstore_service_srv_retr`, `secretstore_service_doc_store` and `secretstore_service_doc_sretr` accordingly);
- `address` - this node will listen to requests from the contract located at this address.

For more information on how every contract works, please refer to [Secret Store service contracts](#secret-store-service-contracts)

### Configuring the Secret Store permissioning contract
Use this option to configure the permissioning contract's address in the Secret Store:
```toml
[secretstore]
acl_contract = "registry"
```

This option can have one of the following values:
- `"none"` - this node will be configured to work without actual permission checks. This means that it will hand out its key shares to anyone requesting it. **This is a testing mode and must NOT be used in production**;
- `"registry"` - the permissioning contract address will be read from the `secretstore_acl_checker` entry in the Registry;
- `address` - permissioning contract resides at given address.

### Configuration file example
Some configuration files examples as well as the scripts for their generation can be found in [Secret Store test repository](https://github.com/svyatonik/sstore_test). Here is one example:
```toml
[secretstore]
disable = false # set to true to disable the overall Secret Store functionality
self_secret = "6c26a76e9b31048d170873a791401c7e799a11f0cefc0171cc31a49800967509"
server_set_contract = "none"
nodes = [
	"d01355a9e8858242ae190208716c48734a5bd2c417b0a9181bc563a18c8ca538cd095d8ab383ed62fba4872c5bd86bad1e82d015e6eabae18546c998d5621923@127.0.0.1:12000",
	"cac6c205eb06c8308d65156ff6c862c62b000b8ead121a4455a8ddeff7248128d895692136f240d5d1614dc7cc4147b1bd584bd617e30560bb872064d09ea325@127.0.0.1:12001"
]
interface = "local"
port = 12000
http_interface = "local"
http_port = 8082
path = "db.poa_ss1/secretstore" # path to the Secret Store keys storage directory
```

## Changing the configuration of a set of servers
To include or exclude a node to/from the Secret Store, two factors must be taken into account:
1) Upon a node exclusion, some secrets could become irrecoverable (there will be less secret shares in the Secret Store than it is required to recover the secret);
2) Upon a node addition, additional shares to the existing secrets should be added to avoid any future situation such as the one described in step 1.

This is where "node set change session" helps. The idea of this session is to **securely** add shares for all the keys on the nodes being added to the Secret Store and wipe the database from the nodes being removed. *Securely* here means that the server/document key is not restored on any node. The implementation of this session is based on the ["Verifiable Secret Redistribution for Threshold Sharing Schemes"](http://www.cs.cmu.edu/~wing/publications/CMU-CS-02-114.pdf) paper.

A so called "administrative" session can only be started by a Secret Store "administrator". The administrator owns the key, which public portion should be known to all nodes. It is configured by adding the following option to the configuration file:
```toml
[secretstore]
admin_public = "002cad13cf0aeb7f5f7c4cda5dd677902c24f2167e0edefdcc4434286825746cf8d46d705b150b006c3a96c905cd9d1a88e4ed61b99212836011c627959fb431"
```

There are 4 kinds of nodes in a "node set change session":
- added nodes: nodes about to be added to the Secret Store;
- removed nodes: nodes about to be removed from the Secret Store;
- stable nodes: nodes staying in the Secret Store.
- isolated nodes: nodes currently offline and disconnected because of failing/misbehaving. They won't provide their shares and won't receive updated shares.

"Nodes set change session" requires all `added`, `removed` and `stable` nodes to be online for the duration of the session. Before starting the session, you'll need to generate two administrator's signatures:
- 'old set' signature: ECDSA signature of all online nodes ids `keccak(ordered_list(staying + added + removing))`;
- 'new set' signature: ECDSA signature of nodes ids, that should stay in the Secret Store after the session ends `keccak(ordered_list(staying + added))`.
To generate these signatures, the Secret Store RPC methods should be used - [`secretstore_serversSetHash`](JSONRPC-secretstore-module#secretstore_serverssethash) and [`secretstore_signRawHash`](JSONRPC-secretstore-module#secretstore_signrawhash). Use following command to start the session:
```bash
curl --data-binary '["0xa7cc7a8ef336189c2bfabfeab8eed55598fa2b480adf98eeb66e006f2811319550222122bd37fb25dbc35709ccd5d9793dc829d208b73ffbce893d63a393101b", "0x54319671ca191b9e08e2064d8b9eaa43cb246e698dc7d995d557ebef3428dc69b93ca3caa20c43552414132448425c9333aa33b2231caa082ddcec7e12a56963"]' -X POST http://localhost:8084/admin/servers_set_change/17374292b9e1a026e1e87fe37ae4e987156d1979461b6daa8ec804ed667e8d100d8129295172a86db63c12b1aa6bb861e89b3ea4330e4b8ae27e0e1917485c1e01/ff5fbcfa6c05d6a1353023e5ade51c3ab53bead7466c1bacd510ee3ef93814ef2a3340cdd7991bea362aac9f111795a470b70d5f4e9ad9877338089c9aacbdb200
```

In this example:
- `["0xa7cc7a8ef336189c2bfabfeab8eed55598fa2b480adf98eeb66e006f2811319550222122bd37fb25dbc35709ccd5d9793dc829d208b73ffbce893d63a393101b", "0x54319671ca191b9e08e2064d8b9eaa43cb246e698dc7d995d557ebef3428dc69b93ca3caa20c43552414132448425c9333aa33b2231caa082ddcec7e12a56963"]` - request body containing the nodes' ids of the 'new set';
- `17374292b9e1a026e1e87fe37ae4e987156d1979461b6daa8ec804ed667e8d100d8129295172a86db63c12b1aa6bb861e89b3ea4330e4b8ae27e0e1917485c1e01` - 'old set' signature;
- `ff5fbcfa6c05d6a1353023e5ade51c3ab53bead7466c1bacd510ee3ef93814ef2a3340cdd7991bea362aac9f111795a470b70d5f4e9ad9877338089c9aacbdb200` - 'new set' signature.

Here is a summary of the actions required to start a "nodes set change session":
1) select the set of nodes and prepare the signatures;
2) stop all running nodes;
3) change the configuration files so that both the nodes from the 'old set' and  the 'new set' are fully connected;
4) start all the nodes both from the 'old set' and the 'new set';
5) start "nodes set change session" and wait for its completion;
6) stop all running nodes;
7) change the configuration files in order to connect the nodes from the 'new set';
8) start all nodes from the 'new set'.

One last note about this session: the key can be lost forever if the number of nodes falls below `t+1` as a result of this session. Avoid any situations when there are a lot of isolated nodes or if the chances for nodes to be removed without replacement are high. *And choose key threshold wisely*.

## Node set contracts
The list of nodes can also be obtained from a blockchain contract, instead of a configuration file. There are two types of such contracts: with and without auto-migration support. Both contracts are supporting at most 256 key servers.

### Node set contracts without auto-migration support
This type of contract is suitable for cases when you do not need to run the [nodes set change session](#changing-servers-set-configuration), or you're going to run it manually. The contract must implement the following interface:
```sol
/// Simple set of key servers.
interface KeyServerSet {
    /// Get number of block when current set has been changed last time.
    function getCurrentLastChange() external view returns (uint256);
    /// Get index of given key server in current set.
    function getCurrentKeyServerIndex(address keyServer) external view returns (uint8);
    /// Get count of key servers in the current set.
    function getCurrentKeyServersCount() external view returns (uint8);
    /// Get address of key server in the current set.
    function getCurrentKeyServer(uint8 index) external view returns (address);

    /// Get all current key servers.
    function getCurrentKeyServers() external view returns (address[]);
    /// Get current key server's public key.
    function getCurrentKeyServerPublic(address keyServer) external view returns (bytes);
    /// Get current key server's address.
    function getCurrentKeyServerAddress(address keyServer) external view returns (string);
}
```

The `disable_auto_migrate` must be set to `true` in the configuration file if you're using this type of the contract.

### Nodes set contracts with auto-migration support
This type of contract is used when you want to auto-start [nodes set change session](#changing-servers-set-configuration) (auto migration) when the set of nodes changes. The contract must implement the following interface:
```sol
/// Set of Key servers with migration support.
interface KeyServerSetWithMigration {
    /// When new server is added to new set.
    event KeyServerAdded(address keyServer);
    /// When existing server is removed from new set.
    event KeyServerRemoved(address keyServer);
    /// When migration is started.
    event MigrationStarted();
    /// When migration is completed.
    event MigrationCompleted();

    /// Get the number of block when current set has been changed last time.
    function getCurrentLastChange() external view returns (uint256);
    /// Get the index of a given key server in the current set.
    function getCurrentKeyServerIndex(address keyServer) external view returns (uint8);
    /// Get the count of key servers in the current set.
    function getCurrentKeyServersCount() external view returns (uint8);
    /// Get the address of a key server in the current set.
    function getCurrentKeyServer(uint8 index) external view returns (address);

    /// Get all the current key servers.
    function getCurrentKeyServers() external view returns (address[]);
    /// Get the current key server's public key.
    function getCurrentKeyServerPublic(address keyServer) external view returns (bytes);
    /// Get the current key server's address.
    function getCurrentKeyServerAddress(address keyServer) external view returns (string);

    /// Get all the migration key servers.
    function getMigrationKeyServers() external view returns (address[]);
    /// Get the migration key server's public key.
    function getMigrationKeyServerPublic(address keyServer) external view returns (bytes);
    /// Get the migration key server's address.
    function getMigrationKeyServerAddress(address keyServer) external view returns (string);

    /// Get all the new key servers.
    function getNewKeyServers() external view returns (address[]);
    /// Get the new key server's public key.
    function getNewKeyServerPublic(address keyServer) external view returns (bytes);
    /// Get the new key server's address.
    function getNewKeyServerAddress(address keyServer) external view returns (string);

    /// Get the migration id.
    function getMigrationId() external view returns (bytes32);
    /// Get the migration master.
    function getMigrationMaster() external view returns (address);
    /// Is migration confirmed by the given node?
    function isMigrationConfirmed(address keyServer) external view returns (bool);
    /// Start the migration.
    function startMigration(bytes32 id) external;
    /// Confirm the migration.
    function confirmMigration(bytes32 id) external;
}
```

An example implementation of this type of contract can be found here: [SetOwnedWithMigration.sol](https://github.com/parity-contracts/secretstore-key-server-set/blob/cf2d4cb4becf7a71686b2d66fa34ac4c5a46d610/contracts/SetOwnedWithMigration.sol). Note that `disable_auto_migrate` must be set to `false` in the configuration file if you're using this type of the contract.

There are 3 sets of servers in this contract:
-  `current`:  is a set currently responsible for serving the Secret Store requests
- `new`: all changes should be made here
- `migration`: not empty during the migration process. 

Auto migration starts when one of the key servers in the `current` set sees that there are differences between the `new` set and the `current` set. Migration starts when `startMigration` method is called. Once the migration is started, the key servers are running [nodes set change session](#changing-servers-set-configuration). When it completes, each key server from the `migration` set needs to confirm that the migration is completed by calling the `confirmMigration` method.

Note that the nodes are sending transactions to this contract. So make sure that every node (from `current`, `new` and `migration` sets) have enough ether to cover the gas costs. Otherwise, the migration might never start or never complete.

## Secret Store service contracts
There are two kinds of participants, sending transactions to the service contracts:
- Secret Store clients, wanting to access the Secret Store API;
- Secret Store nodes, responding to the service contracts requests.

This implies different contract interfaces for different participant types - one is `ClientApi` and the other is `KeyServerApi`. The key servers are sending transactions to the service contracts, so please make sure that the key servers accounts have enough ether to cover gas costs. [The reference implementation]
(https://github.com/parity-contracts/secretstore-service/pull/1/files) of the service contracts requires a fee to be paid for every request. The fee is split among all key servers and can be transferred to their accounts by calling the `drain` method. This can be used as a source of ether to pay for the transactions costs.

This section will only concern the `ClientApi` interfaces. The common features of these interfaces are the method to register a request and some events signaling that a request has been served or has failed to be served. Some more information about the failure can be found in the nodes' logs.

Another important note is that the [reference implementation](https://github.com/parity-contracts/secretstore-service/pull/1/files) reads the list of key servers from the [nodes set contract](#nodes-set-contracts). You will not be able to use the service contracts if the set of nodes is setup using a configuration file.

### Server Key generation service contract
Call the `generateServerKey` method and wait until a key is either generated (`ServerKeyGenerated`), or a generation error occurs (`ServerKeyGenerationError`):
```sol
/// Server Key generation service contract API (client view).
interface ServerKeyGenerationServiceClientApi {
    /// When a server key is generated.
    event ServerKeyGenerated(bytes32 indexed serverKeyId, bytes serverKeyPublic);
    /// When an error occurs during the server key generation.
    event ServerKeyGenerationError(bytes32 indexed serverKeyId);

    /// Request a new server key generation. The generated key will be published via ServerKeyGenerated event when available.
    function generateServerKey(bytes32 serverKeyId, uint8 threshold) external payable;
}
```

### Server Key retrieval service contract
Call the `retrieveServerKey` method and wait until the key is either retrieved (`ServerKeyRetrieved`), or a generation error occurs (`ServerKeyRetrievalError`):
```sol
/// Server Key retrieval service contract API (client view).
interface ServerKeyRetrievalServiceClientApi {
    /// When the server key is retrieved.
    event ServerKeyRetrieved(bytes32 indexed serverKeyId, bytes serverKeyPublic, uint256 threshold);
    /// When an error occurs during the server key retrieval.
    event ServerKeyRetrievalError(bytes32 indexed serverKeyId);

    /// Retrieve the existing server key. The retrieved key will be published via ServerKeyRetrieved or ServerKeyRetrievalError.
    function retrieveServerKey(bytes32 serverKeyId) external payable;
}
```

### Document Key store service contract
Call the `storeDocumentKey` method and wait until key is either stored (`DocumentKeyStored`), or storing error occurs (`DocumentKeyStoreError`):
```sol
/// Document Key store service contract API (client view).
interface DocumentKeyStoreServiceClientApi {
    /// When a document key is stored.
    event DocumentKeyStored(bytes32 indexed serverKeyId);
    /// When an error occurs during the document key store.
    event DocumentKeyStoreError(bytes32 indexed serverKeyId);

    /// Request a document's key store. Use `secretstore_generateDocumentKey` RPC to generate both
    /// `commonPoint` and `encryptedPoint`.
    function storeDocumentKey(bytes32 serverKeyId, bytes commonPoint, bytes encryptedPoint) external payable;
}
```

### Document Key shadow retrieval service contract
In this client API, the request is registered by calling the `retrieveDocumentKeyShadow` method. Retrieval consists in 2 stages: first, the 'common' data is retrieved and published via the `DocumentKeyCommonRetrieved` event. The common data includes `commonPoint` and `threshold`. Once the common data was retrieved, the client must listen for the `DocumentKeyPersonalRetrieved` events. Once there are exactly `threshold+1` events with **the same** `decryptedSecret` value, you can pass `commonPoint`, `decryptedSecret` and all `shadows` from gathered `DocumentKeyPersonalRetrieved` events to the `secretstore_shadowDecrypt` RPC method to decrypt the encrypted data.
```sol
/// Document Key shadow retrieval service contract API (client view).
interface DocumentKeyShadowRetrievalServiceClientApi {
    /// When a document key common portion is retrieved. Once this event is fired, wait for
    /// exactly `threshold+1` `DocumentKeyPersonalRetrieved` events with the same `decryptedSecret` value.
    event DocumentKeyCommonRetrieved(bytes32 indexed serverKeyId, address indexed requester, bytes commonPoint, uint8 threshold);
    /// When a document key personal portion is retrieved. Once enough events were fired, use `secretstore_shadowDecrypt`
    /// to decrypt the document contents.
    event DocumentKeyPersonalRetrieved(bytes32 indexed serverKeyId, address indexed requester, bytes decryptedSecret, bytes shadow);
    /// When an error occurs during the document key retrieval.
    event DocumentKeyShadowRetrievalError(bytes32 indexed serverKeyId, address indexed requester);

    /// Request a document key retrieval.
    function retrieveDocumentKeyShadow(bytes32 serverKeyId, bytes requesterPublic) external payable;
}
```

### ECDSA signature generation contract
*In development*

### Service contracts on Kovan
All the service contracts described above are deployed on the Kovan testnet (**testnet means that it should be used for tests only - please do not rely on this as an infrastructure for any production application**). This gives you the ability to store encryption keys even without deploying your own Secret Store. Here is the information required to use these contracts:
- latest deployment information (including the service contract addresses) can be found in [secretstore-service repository](https://github.com/parity-contracts/secretstore-service/blob/165b6083c5f29d8baa01bffad5e0a7d7b365dae0/README.md) ;
- there are currently 5 key servers deployed (so maximum `threshold` is 4);
- all the deployed key servers are configured to use [OnceTransferablePermission.sol](https://github.com/parity-contracts/secretstore-acl/blob/17dd8f3f97f601723b3e13f11d515e4ebb457b95/contracts/OnceTransferablePermission.sol) permissioning contract (it is a contract with artificially limited abilities to use in tests only), with the deployment data published [here](https://github.com/parity-contracts/secretstore-acl/blob/17dd8f3f97f601723b3e13f11d515e4ebb457b95/README.md). So, before generating key with new ID, please make sure that you have 'reserved' the key id for your own account by calling  `createKey`;
- processing the requests could take some time (up to 10 blocks for [Document Key shadow retrieval](#document-key-shadow-retrieval-service-contract) requests and 4-5 blocks for all other requests). It could be even more when key server misses the request (because of synchronization). As a rule of thumb - if your request wasn't processed for >100 blocks, then something is definitely wrong;
- you are not the only user of this Secret Store, so expect keys with IDs like '0x0000000000000000000000000000000000000000000000000000000000000001' to be occupied and some requests to fail because of this.

If you experience any problem with the service contracts on Kovan, please report it on our [Discord Support Channel](https://discord.io/openethereum).
