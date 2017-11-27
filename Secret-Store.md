The Parity Secret Store is core technology that enables distributed key generation, distributed key storage and threshold retrieval according to blockchain permissions. Secret store is a set of computers (aka key servers), which are running the Parity Ethereum client in special mode. Secret store can be used to distributively generate a server-side key pair, without revealing the secret portion of this key pair to any individual key server. Implementation is based on [ECDKG: A Distributed Key Generation Protocol Based on Elliptic Curve Discrete Logarithm](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.124.4128&rank=1). 

The same secret can be used later to encrypt some confidential data. To encrypt some confidential document, you could:
- in addition to server-side key, use command-line tools to generate document key (see below);
- ask Secret store to distributively store this key. Document key is also not revealed to any individual key server and is stored in encrypted form;
- encrypt document with document key and store it.
When original content of the document is required, you can ask any key server to restore the secret and use it for decryption. This call will only succeed, if requester (the one who owns the key, used to generate “restore the secret” request) has an access to the document. List of users, able to access the secret is stored in the permissioning contract on the blockchain.

Server-side key pair also can be used to generate EC-Schnorr signature for given message (message hash). Implementation is based on [Efficient multi-party digital signature using adaptive secret sharing for low-power devices in wireless networks](https://www.researchgate.net/profile/A_Chronopoulos/publication/224386852_Efficient_multi-party_digital_signature_using_adaptive_secret_sharing_for_low-power_devices_in_wireless_networksPLEASE_REFERENCE_IN_YOUR_PAPERS/links/00b7d52c2a3fb5bf8e000000/Efficient-multi-party-digital-signature-using-adaptive-secret-sharing-for-low-power-devices-in-wireless-networksPLEASE-REFERENCE-IN-YOUR-PAPERS.pdf) with [Notes on Paper “Efficient Multi-party Digital Signature using Adaptive Secret
Sharing for Low-Power Devices in Wireless Networks”](http://www.wu.ece.ufl.edu/mypapers/notesMulti-partyDigitalSignature.pdf).

## Permissioning contract
The permissioning contract has to implement a single method:
```
function checkPermissions(address user, bytes32 document) constant returns (bool) {}
```
When `true` then the secret will be collected and returned to the user, otherwise an exception is returned.

## Setup
### 1: Build
```
git clone https://github.com/paritytech/parity.git
cd parity
cargo build --features secretstore --release
```
### 2: Create config files
Before starting configuration files creation, you must choose the main parameters of the secret store. These are N – the number of nodes in the secret store and T (less than N) – threshold. Threshold is the maximal number of nodes, which **can not** be used to restore collectively generated secret. If there are T malicious nodes in the secret store, they won’t be able to restore the secret, but T + 1 nodes they could.

There are few configuration schemes, which must be avoided:
- schemes with N = 1: there is only one key server in cluster, so secret can be restored by this key server at anytime, without any request;
- schemes with T = 0: every node can recover the secret on its own.

Every key server holds its own key pair, which is used to encrypt outgoing network messages. To generate these key pairs, you can use OpenSSL:
```
openssl ecparam -name secp256k1 -genkey -noout | openssl ec -text -noout > keyserver.key
```
Then, to read private key from generated file:
```
cat keyserver.key | grep priv -A 3 | tail -n +2 | tr -d '\n[:space:]:' | sed 's/^00//'
```
Then, to read public key from generated file:
```
cat keyserver.key | grep pub -A 5 | tail -n +2 | tr -d '\n[:space:]:' | sed 's/^04//'
```

After generating key pairs, for every key server, you should create configuration file like this:
```
[parity]
chain = "kovan"

[secretstore]
disable = false
self_secret = "6c26a76e9b31048d170873a791401c7e799a11f0cefc0171cc31a49800967509"
nodes = ["cac6c205eb06c8308d65156ff6c862c62b000b8ead121a4455a8ddeff7248128d895692136f240d5d1614dc7cc4147b1bd584bd617e30560bb872064d09ea325@127.0.0.1:8085", "6b8d9b9ecde2f0d8c7b2685dd10d181000fa0b26462674dece3096488d6b8d6e3c0e4e1262e3f0eb3b997783b3d6471c281905c5fafeb7908f3aeb7326274db6@127.0.0.1:8087"]
```

In each configuration file change:  
- `self_secret` is the private key of this key server
- `nodes` are identifiers of all other key servers of this secret store in the form KEY_SERVER_PUBLIC@IP_ADDR:PORT

Additional possible fields under `[secretstore]`:
```
interface = "local"
port = 8083
http_interface = "local"
http_port = 8082
path = "db.kovan_ss1/secretstore"
```

Here, path is a path for the internal storage of this KeyServer data. KeyServer will use TCP `port` on `interface` for its internal communications with other key servers. TCP `http_port` on `http_interface` will be used for listening and answering to secret generation and retrieval requests, described below.

### 3: Run the Key Servers
target/release/parity` has been built on step 1. Copy this file to the folder with configuration file and run it:
```
RUST_LOG=secretstore=trace,secretstore_net=trace ./parity --config config.toml
```

### 4: Generate server-side key pair
```
curl -v -X POST http://localhost:8082/shadow/0000000000000000000000000000000000000000000000000000000000000000/de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00/1
```

Where:
- `0000000000000000000000000000000000000000000000000000000000000000` is 32-bytes identifier of server-side key pair. The same identifier should be used later to recover the key. If you're going to encrypt document with generated key, it would be good to use document contents hash as this identifier.
- `de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00` is the same identifier, signed with requester private key
- `1` is key generation threshold (configuration parameter T, described in configuration section above)

Return value is a hex-encoded public portion of server-side key pair.

### 5: Store externally-generated document key
```
curl -v -X POST http://localhost:8082/shadow/0000000000000000000000000000000000000000000000000000000000000000/de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00/368244efaf441c2dabf7a723355a97b3b86f27bdb2827ae6f34ddece5132efd37af4ba808957b7113b4296bc4ae9ec7be38f9de6bae00504e775883a50d4658a/b7ad0603946987f1a154ae7f074e45da224eaa83704aac16a2d43a675d219654cf087b5d7aacce0790a65abbc1a495b26e71a5c6e9a4a71b543bf0048935bc13
```

Where:
- `0000000000000000000000000000000000000000000000000000000000000000` is server-side key pair identifier
- `de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00` is the same identifier, signed with requester private key
`368244efaf441c2dabf7a723355a97b3b86f27bdb2827ae6f34ddece5132efd37af4ba808957b7113b4296bc4ae9ec7be38f9de6bae00504e775883a50d4658a` is 'common point' part of encrypted document key
`b7ad0603946987f1a154ae7f074e45da224eaa83704aac16a2d43a675d219654cf087b5d7aacce0790a65abbc1a495b26e71a5c6e9a4a71b543bf0048935bc13` is 'encrypted point' part of encrypted document key

RPCs, which can be used to generate 'common point' and 'encrypted point' will be added later.

### 6: Simultaneously generate server-side and document key
```
curl -X POST http://localhost:8082/0000000000000000000000000000000000000000000000000000000000000001/a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01/0
```

Where:
- `0000000000000000000000000000000000000000000000000000000000000001` is 32-bytes identifier of key generation session. The same identifier should be used later to recover the key. If you're going to encrypt document with generated key, it would be good to use document contents hash as this identifier.
-
 `a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01` is key generation session identifier, signed with requester private key
- `0` is key generation threshold (configuration parameter T, described in configuration section above)

Return value is a hex-encoded point on secp256k1 (key), encrypted with requester public key. You could manually generate key from this point (usually by taking X coordinate of the point) and encrypt data, or use one of RPCs described below.

### 7: Retrieve a secret
```
curl http://localhost:8082/0000000000000000000000000000000000000000000000000000000000000001/a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01
```
- `0000000000000000000000000000000000000000000000000000000000000001` is the identifier, used previously in key generation request
-
 `a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01` is the same identifier, signed with requester private key

Return value is a hex-encoded point on secp256k1 (key), encrypted with requester public key. You could manually restore key from this point (usually by taking X coordinate of the point) and decrypt the data, or use one of RPCs below.

Please notice, that the during this call, secret is revealed to the key server, you are requesting. To make sure that secret is not revealed to any key server, you could use method, described in section "Retrieve a secret shadow".

### 8: Retrieve a secret shadow

```
curl http://localhost:8082/shadow/0000000000000000000000000000000000000000000000000000000000000001/a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01
```
- `0000000000000000000000000000000000000000000000000000000000000001` is the identifier, used previously in key generation request
-
 `a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01` is the same identifier, signed with requester private key

This method is suitable for cases, when you do not trust the key server, you're asking for the key. Secret is not revealed to this key server. It only responds with the data, required to restore secret if you have private key of requester.

Return value is a JSON object with `decrypted_secret`, `common_point` and `decrypt_shadows` fields. To decrypt this data and restore the secret, you must use `secretstore_shadowDecrypt` RPC, described below. Pass values of the fields, described above to this RPC and it will return the secret.

### 9: Sign message using server-side key pair
```
curl -v http://localhost:8082/0000000000000000000000000000000000000000000000000000000000000000/de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00/0000000000000000000000000000000000000000000000000000000000000001
```

Where:
- `0000000000000000000000000000000000000000000000000000000000000000` is server-side key pair identifier
- `de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00` is the same identifier, signed with requester private key
`0000000000000000000000000000000000000000000000000000000000000001` is hash of the message, which should be signed with server-side key

Return value is hex-encoded signature of the message, encrypted with caller public key. Please note, that this call will only succeed if requester have access to the server-side secret key (it is checked by calling permissioning contract' check method).

## RPCs for handling the secrets

3 special RPC methods are present:
```
secretstore_encrypt(account, password, encrypted_key, plain_data);
secretstore_decrypt(account, password, encrypted_key, encrypted_data);
secretstore_shadowDecrypt(account, password, decrypted_secret, common_point, decrypt_shadows, encrypted_data);
```
Where:
```
account - parity account of KeyServer requester
password - password for the account
encrypted_key - document encryption key, encrypted with requester public key. It is response of KeyServer generate_document_key() && document_key() REST-methods
plain_data - bytes to encrypt
encrypted_data - bytes to decrypt. It is response of Parity secretstore_encrypt RPC-method
decrypted_secret, common_point, decrypt_shadows - parts of KeyServer document_key_shadow() method response
```
So to encrypt document:
1) Call `KeyServer::generate_document_key()` method:
```
curl -X POST http://localhost:8082/0000000000000000000000000000000000000000000000000000000000000000/de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00/0
```
2) Call `Parity::secretstore_encrypt()` method:
```
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_encrypt", "params": ["0x00dfE63B22312ab4329aD0d28CaD8Af987A01932", "password", "0x047155813de218725ceffb7e6c3dbdf5af4273b96de42552d4575f9de32001c4c2337b685288ee3411b54c5c0ace68d890227b6bc2fa80e52edbd2334bec0a45c9efe989eea16ad26ddf592dd5df55403d475a109350275021676624bb7ee06671f679c275ee26173d6f48030d77ae646b5c6a83ebe5e240b81e5286fa6960f5a998f54ee474f9146820a4ad3260d77bb04660dbbc3492deed8c7a92f138175851f1fb5e565d86464c5eacce7766dc2a7f", "0xdeadbeef"], "id":1 }' -H 'content-type: application/json;' http://127.0.0.1:8545/
```

To decrypt document (simple version, but document secret is revealed to one of KeyServers):
1) Call `KeyServer::document_key()` method:
```
curl http://localhost:8082/0000000000000000000000000000000000000000000000000000000000000000/de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00
```
2) Call `Parity::secretstore_decrypt()` method:
```
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_decrypt", "params": ["0x00dfE63B22312ab4329aD0d28CaD8Af987A01932", "password", "0x047155813de218725ceffb7e6c3dbdf5af4273b96de42552d4575f9de32001c4c2337b685288ee3411b54c5c0ace68d890227b6bc2fa80e52edbd2334bec0a45c9efe989eea16ad26ddf592dd5df55403d475a109350275021676624bb7ee06671f679c275ee26173d6f48030d77ae646b5c6a83ebe5e240b81e5286fa6960f5a998f54ee474f9146820a4ad3260d77bb04660dbbc3492deed8c7a92f138175851f1fb5e565d86464c5eacce7766dc2a7f", "0x2ddec1f96229efa2916988d8b2a82a47ef36f71c"], "id":1 }' -H 'content-type: application/json;' http://127.0.0.1:8545/
```

To decrypt document (extended version, document secret is not revealed to any of KeyServer, except for 1-of-1 case):
1) Call `KeyServer::document_key_shadow()` method:
```
curl http://localhost:8082/shadow/0000000000000000000000000000000000000000000000000000000000000000/de12681e0b8f7a428f12a6694a5f7e1324deef3d627744d95d51b862afc13799251831b3611ae436c452b54cdf5c4e78b361a396ae183e8b4c34519e895e623c00
```
2) Call `Parity::secretstore_shadowDecrypt()` method:
```
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_shadowDecrypt", "params":["0x00dfE63B22312ab4329aD0d28CaD8Af987A01932", "password", "0x843645726384530ffb0c52f175278143b5a93959af7864460f5a4fec9afd1450cfb8aef63dec90657f43f55b13e0a73c7524d4e9a13c051b4e5f1e53f39ecd91","0x07230e34ebfe41337d3ed53b186b3861751f2401ee74b988bba55694e2a6f60c757677e194be2e53c3523cc8548694e636e6acb35c4e8fdc5e29d28679b9b2f3",["0x049ce50bbadb6352574f2c59742f78df83333975cbd5cbb151c6e8628749a33dc1fa93bb6dffae5994e3eb98ae859ed55ee82937538e6adb054d780d1e89ff140f121529eeadb1161562af9d3342db0008919ca280a064305e5a4e518e93279de7a9396fe5136a9658e337e8e276221248c381c5384cd1ad28e5921f46ff058d5fbcf8a388fc881d0dd29421c218d51761"],"0x2ddec1f96229efa2916988d8b2a82a47ef36f71c"], "id": 1}' -H 'content-type: application/json;' http://127.0.0.1:8545/
```


## Configuration options
### Configuring list of key servers using KeyServerSet contract
In the example above we have configured set of key servers using configuration files. This method is simple && works fine until you decide to add/remove KeyServer from the Secret Store. To make this happen, you should first stop all key servers, edit configuration files (manually including/excluding key server(s) from the set) and restart key servers.
There's another way, which makes this kind if changes easier. You could just deploy [KeyServerSet](https://github.com/paritytech/contracts/blob/master/KeyServerSet.sol) contract and register it under 'secretstore_server_set' name in the registry.
Upon deployment, you should fill the contract with key servers set addresses using `addKeyServer` method, passing public key of key server and address of key server (in IP:port form).
Once this is done, you could easily change Secret Store configuration, by calling `addKeyServer` && `removeKeyServer` methods of this contract.

**IMPORTANT**: please notice that keys, generated using scheme t-of-N are only recoverrable when there are at lease t+1 share holders (i.e. key servers). So if you're going to remove key server from the Secret Store, ensure that there are no keys which will became irrrcoverable after this change. Alternatively, you could use 'key servers set change' session, decribed in the next section.

### Key Servers Set change session
You might (and should) want to ensure that all keys are remaining recoverrable **before** removing key server(s) from secret store. You also might want to add new shares for existing key when new key server(s) is/are getting added to the Secret Store (so instead of having t-of-oldN key sharing scheme, you'll get t-of-newN sharing scheme).
That's why there's a special 'key servers set change' session, which main duty is to ensure that no secrets are lost when key server is removed from secret store and all new key servers are getting new shares.
This session can only be started when **all** key servers (including both servers that are being **added** and **removed**) are configured to be the single Secret Store (either via config files, or the contract).
So steps to run this session are:
1) given current nodes set OLD_NODES, select NODES_TO_REMOVE (must be in OLD_NODES set) and NODES_TO_ADD (must not be in OLD_NODES)
2) add NODES_TO_ADD to the Secret Store either by editing configuration files, or by calling `KeyServersSet` contract methods
3) run `Key Servers Set change` session (see below)
4) remove NODES_TO_REMOVE from the secret sytore either by editing configuration files, or by calling `KeyServersSet` contract methods

You can start this session manually by issuing HTTP Post request to the {key_server_url}/admin/servers_set_change/{old_signature}/{new_signature} wth {BODY}, where:
`key_server_url` - is the url of your key server (any)
`old_signature` - is the signed hash of ordered publics of all key servers, which are currently making this Secret Store (after step 2 - i.e. OLD_NODES + NODES_TO_ADD)
`new signature` - is the signed hash of ordered publics of all key servers, of 'new' version of Secret Store (after step 4 - i.e. OLD_NODES + NODES_TO_ADD - NODES_TO_REMOVE)
`BODY` - is the json array of hex-encoded nodes publics of all key servers, of 'new' version of Secret Store (after step 4)

`old_signature` and `new_signature` must be a signatures of Secret Store 'administrator'. It is an entitiy, which can start 'administrative' sessions (sessions which affect the whole Secret Store).
Its public is provided via `--secretstore-admin-public` command line argument. All key servers must have the samed `admin_public`, or else session won't start.

Example call:
```
curl --data-binary '["0xa7cc7a8ef336189c2bfabfeab8eed55598fa2b480adf98eeb66e006f2811319550222122bd37fb25dbc35709ccd5d9793dc829d208b73ffbce893d63a393101b", "0x54319671ca191b9e08e2064d8b9eaa43cb246e698dc7d995d557ebef3428dc69b93ca3caa20c43552414132448425c9333aa33b2231caa082ddcec7e12a56963"]' -v -X POST http://localhost:8084/admin/servers_set_change/17374292b9e1a026e1e87fe37ae4e987156d1979461b6daa8ec804ed667e8d100d8129295172a86db63c12b1aa6bb861e89b3ea4330e4b8ae27e0e1917485c1e01/ff5fbcfa6c05d6a1353023e5ade51c3ab53bead7466c1bacd510ee3ef93814ef2a3340cdd7991bea362aac9f111795a470b70d5f4e9ad9877338089c9aacbdb200
```

The code for calculating both `old_signature` and `new_signature` is here:
https://github.com/paritytech/parity/blob/359b4de3ff5407fb0b0694ac810c9a30eedb2c46/secret_store/src/key_server_cluster/jobs/servers_set_change_access_job.rs#L139
We're currently working on automated start of Key Servers Set change session, when set of key servers is configured using contract.
If you want to run this session right now, or in case when servers set is configured using configuration files, please compile this code, or file an issue for adding an RPC to Parity.