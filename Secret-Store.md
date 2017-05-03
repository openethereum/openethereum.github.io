Secret store is a set of computers (aka key servers), which are running Parity in special mode. Secret store can be used to distributively generate a secret, without revealing this secret to any individual key server. Implementation is based on [this paper](http://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=A0EF4DEE6638E535648F19C13A0251C2?doi=10.1.1.124.4128&rep=rep1&type=pdf).

The same secret can be used later to encrypt some confidential data (document). When original content of the document is required, you can ask any key server to restore the secret and use it for decryption. This call will only succeed, if requester (the one who owns the key, used to generate “restore the secret” request) has an access to the document. List of users, able to access the secret is stored in the permissioning contract on the blockchain.

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
### 4: Generate a secret
```
curl -X POST http://localhost:8082/0000000000000000000000000000000000000000000000000000000000000001/a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01/0
```
Where:  
- `0000000000000000000000000000000000000000000000000000000000000001` is 32-bytes identifier of key generation session. The same identifier should be used later to recover the key. If you're going to encrypt document with generated key, it would be good to use document contents hash as this identifier.
-
 `a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01` is key generation session identifier, signed with requester private key
- `0` is key generation threshold (configuration parameter T, described in configuration section above)

Return value is a hex-encoded point on secp256k1 (key), encrypted with requester public key. You could manually generate key from this point (usually by taking X coordinate of the point) and encrypt data, or use one of RPCs described below.

### 5: Retrieve a secret
```
curl http://localhost:8082/0000000000000000000000000000000000000000000000000000000000000001/a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01
```
- `0000000000000000000000000000000000000000000000000000000000000001` is the identifier, used previously in key generation request
-
 `a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01` is the same identifier, signed with requester private key

Return value is a hex-encoded point on secp256k1 (key), encrypted with requester public key. You could manually restore key from this point (usually by taking X coordinate of the point) and decrypt the data, or use one of RPCs below.

Please notice, that the during this call, secret is revealed to the key server, you are requesting. To make sure that secret is not revealed to any key server, you could use method, described in section "Retrieve a secret shadow".

### 6: Retrieve a secret shadow

```
curl http://localhost:8082/shadow/0000000000000000000000000000000000000000000000000000000000000001/a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01
```
- `0000000000000000000000000000000000000000000000000000000000000001` is the identifier, used previously in key generation request
-
 `a199fb39e11eefb61c78a4074a53c0d4424600a3e74aad4fb9d93a26c30d067e1d4d29936de0c73f19827394a1dd049480a0d581aee7ae7546968da7d3d1c2fd01` is the same identifier, signed with requester private key

This method is suitable for cases, when you do not trust the key server, you're asking for the key. Secret is not revealed to this key server. It only responds with the data, required to restore secret if you have private key of requester.

Return value is a JSON object with `decrypted_secret`, `common_point` and `decrypt_shadows` fields. To decrypt this data and restore the secret, you must use `secretstore_shadowDecrypt` RPC, described below. Pass values of the fields, described above to this RPC and it will return the secret.

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