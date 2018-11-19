---
title: The `secretstore` Module
---

Parity has separate RPC API set - secretstore, which:

- is available in default Parity client (i.e. does not requires Parity to be built with `--features secretstore`);
- is considered unsafe and must be enabled separately (by passing secretstore, all or safe as an `--jsonrpc-apis` argument value);
- contains dangerous methods and must be enabled with caution.

## JSON-RPC methods

- [secretstore_decrypt](#secretstore_decrypt)
- [secretstore_encrypt](#secretstore_encrypt)
- [secretstore_generateDocumentKey](#secretstore_generatedocumentkey)
- [secretstore_serversSetHash](#secretstore_serverssethash)
- [secretstore_shadowDecrypt](#secretstore_shadowdecrypt)
- [secretstore_signRawHash](#secretstore_signrawhash)

## JSON-RPC API Reference

### secretstore_decrypt

This method can be used to decrypt document, encrypted by `secretstore_encrypt` method.

#### Parameters

0. `Address` - address of account, which was used as requester in document [key retrieval session](Secret-Store#document-key-retrieval-session);
0. `String` - password for the passed account;
0. `Data` - Data returned by [document key retrieval session](Secret-Store#document-key-retrieval-session);
0. `Data` - the encrypted document data (result of `secretstore_encrypt` call);

```js
params: [
  "0x00a329c0648769A73afAc7F9381E08FB43dBEA72",
  "",
  "0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4",
  "0xd164666d070be4d527285a40e84e8d17bf3e88fc"
]
```

#### Returns

- `Data` - Decrypted document

#### Example

Request
```bash
curl --data '{"method":"secretstore_decrypt","params":["0x00a329c0648769A73afAc7F9381E08FB43dBEA72","","0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4","0xd164666d070be4d527285a40e84e8d17bf3e88fc"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xdeadbeef"
}
```

***

### secretstore_encrypt

This method can be used after running document key retrieval session or server and document key generation session. You can use it to encrypt small document:

#### Parameters

0. `Address` - address of account, which was used as requester in document [key retrieval session](Secret-Store#document-key-retrieval-session);
0. `String` - password for the passed account;
0. `Data` - Data returned by [document key retrieval session](Secret-Store#document-key-retrieval-session);
0. `Data` - hex-encoded document data;

```js
params: [
  "0x00a329c0648769A73afAc7F9381E08FB43dBEA72",
  "",
  "0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4",
  "0xdeadbeef"
]
```

#### Returns

- `Data` - Encrypted document

#### Example

Request
```bash
curl --data '{"method":"secretstore_encrypt","params":["0x00a329c0648769A73afAc7F9381E08FB43dBEA72","","0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4","0xdeadbeef"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xd164666d070be4d527285a40e84e8d17bf3e88fc"
}
```

***

### secretstore_generateDocumentKey

This method is used to securely generate document key, so that it remains unknown to all key servers.

#### Parameters

0. `Address` - address of account, which was used as requester in document [key retrieval session](Secret-Store#document-key-retrieval-session);
0. `String` - password for the passed account;
0. `Data` - Data returned by [document key retrieval session](Secret-Store#document-key-retrieval-session);

```js
params: [
  "0x00a329c0648769A73afAc7F9381E08FB43dBEA72",
  "",
  "0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4"
]
```

#### Returns

- `Object` - All required data to encrypt your document
    - `common_point`:   `Data` - To be used in [document key storing session](Secret-Store#document-key-storing-session) in conjunction with `encrypted_point` to safely store document key in the Secret Store
    - `encrypted_key`:   `Data` - Encryption key to use `secretstore_encrypt`
    - `encrypted_point`:   `Data` - To be used in [document key storing session](Secret-Store#document-key-storing-session) in conjunction with `common_point` to safely store document key in the Secret Store

#### Example

Request
```bash
curl --data '{"method":"secretstore_generateDocumentKey","params":["0x00a329c0648769A73afAc7F9381E08FB43dBEA72","","0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "common_point": "0xff67022701f180a881a0fb7ce6a0370e64c9a1c7b2d4429fb4b5f41b2e74520289807769a02d5b6a35fa2c41b420d26187d7f8a0ea1f6cc19346f7b6208dbdac",
    "encrypted_key": "0x0468bb2e0b7ebbdd7d3d43372a653561c3fca0361aa18054156ed21ceeb3b424b0b7fc52fd4c99a8346156b7d45bc81fc556027276c07663a71dd30d7ec9626883729ea93d7f854352c7a8c36db0027c6be68d89f8ea6e71a35e72a2fdf646b0b69eb76ffc9ccc0725835fcfee3bad65206c6ac2048ea120e394bbdfb46d08d82cb0db03035fdf783d4a594fe407e9d67da94dd8eda706d4a52d9910a018b52908fc4dc016ec48f6ef099ade946192c781",
    "encrypted_point": "0xed6ab24f91afd8f5c2a6d98e2ea1cfeb9bafff83f02a659628d7684c0f0453706d041adc414e7bb63ae24bf54a39eb0e6a387235e2463aabe788b92ca4ca234d"
  }
}
```

***

### secretstore_serversSetHash

Computes the hash of nodes ids, required to compute nodes set signature for manual [nodes set change session](Secret-Store-Configuration#changing-servers-set-configuration)

#### Parameters

0. `Array` - Set of node id

```js
params: [
  [
    "0x843645726384530ffb0c52f175278143b5a93959af7864460f5a4fec9afd1450cfb8aef63dec90657f43f55b13e0a73c7524d4e9a13c051b4e5f1e53f39ecd91",
    "0x07230e34ebfe41337d3ed53b186b3861751f2401ee74b988bba55694e2a6f60c757677e194be2e53c3523cc8548694e636e6acb35c4e8fdc5e29d28679b9b2f3"
  ]
]
```

#### Returns

- `Data` - the hash of nodes ids

#### Example

Request
```bash
curl --data '{"method":"secretstore_serversSetHash","params":[["0x843645726384530ffb0c52f175278143b5a93959af7864460f5a4fec9afd1450cfb8aef63dec90657f43f55b13e0a73c7524d4e9a13c051b4e5f1e53f39ecd91","0x07230e34ebfe41337d3ed53b186b3861751f2401ee74b988bba55694e2a6f60c757677e194be2e53c3523cc8548694e636e6acb35c4e8fdc5e29d28679b9b2f3"]],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x67d808b1c7a0e5521d6e1b91c9604eaa303e5cd40149fc7af5731e0274845357"
}
```

***

### secretstore_shadowDecrypt

This method can be used to decrypt document, encrypted by `[secretstore_encrypt](#secretstore_encrypt)` method before.

#### Parameters

0. `Address` - address of account, which was used as requester in document [key retrieval session](Secret-Store#document-key-retrieval-session);
0. `String` - password for the passed account;
0. `Data` - the value of `decrypted_secret` field from [document key shadow retrieval](Secret-Store#document-key-shadow-retrieval-session) session result;
0. `Data` - the value of `common_point` field from [document key shadow retrieval](Secret-Store#document-key-shadow-retrieval-session) session result;
0. `Array` - the value of `decrypt_shadows` field from [document key shadow retrieval](Secret-Store#document-key-shadow-retrieval-session) session result;
0. `Data` - the encrypted document data (result of `secretstore_encrypt` call);

```js
params: [
  "0xfeacd0d28fd158ba2d3adb6d69d20c723214edc9",
  "bobpwd",
  "0x9b5aa977f537d24c5f523f67a95329bdd147e6be1b0d913c1506d2a0a210ab24ce380787d9b81f88fd05dfcfc083c8df56569a763440a1159a41db144a0d3d6b",
  "0xf0e62b05b68b1847ad948572a1b04a91dee7d7dca2f675fd00c136eb706d4916fb1fcdd446ab9df236eba3ab8d6184b7b3f4e8584259b5e2dc6dff8bcb07c632",
  [
    "0x0478055ba0c544032560c4db1fbe02cd848217a9d9e476dc3a4f7f2c6dbe9535f64b947d813a42e77a3d21ccbd46a50f10c6a556daa897ed4e80d9938f696b2efde9558da7a1e0c2290fc97d0594a134a2a2fc316250808fb43e42bcfb3586e74a97dde2c6403f25b0952e15e7b2a4d11dab01f1d77d0e39fc98a83bf2971e190bed38108dfe9f6be7c29f9fe1c868df7c",
    "0x04733d896fc8279b3c8209e10dd12f7c052ac9d8f171dff174e2183f68720294b162e879166ae744883c74cbe56528c2908a4d17c6f245d9158491351cc1f11ecf79f8e9b828963e07c839eaf923c2db29d2c85d282326f83e9ccac334e3abf3e99b7e41811940426f97995494e2bae53f0ddd38ccd6dba26847723a77629f703c564c14da4880521e192976e09e7499a4"
  ],
  "0x237ac394e3f6cbe7395fc7076a3b58036a0e185a519e41b35a87ba73679cc1bb"
]
```

#### Returns

- `Data` - The decrypted document

#### Example

Request
```bash
curl --data '{"method":"secretstore_shadowDecrypt","params":["0xfeacd0d28fd158ba2d3adb6d69d20c723214edc9","bobpwd","0x9b5aa977f537d24c5f523f67a95329bdd147e6be1b0d913c1506d2a0a210ab24ce380787d9b81f88fd05dfcfc083c8df56569a763440a1159a41db144a0d3d6b","0xf0e62b05b68b1847ad948572a1b04a91dee7d7dca2f675fd00c136eb706d4916fb1fcdd446ab9df236eba3ab8d6184b7b3f4e8584259b5e2dc6dff8bcb07c632",["0x0478055ba0c544032560c4db1fbe02cd848217a9d9e476dc3a4f7f2c6dbe9535f64b947d813a42e77a3d21ccbd46a50f10c6a556daa897ed4e80d9938f696b2efde9558da7a1e0c2290fc97d0594a134a2a2fc316250808fb43e42bcfb3586e74a97dde2c6403f25b0952e15e7b2a4d11dab01f1d77d0e39fc98a83bf2971e190bed38108dfe9f6be7c29f9fe1c868df7c","0x04733d896fc8279b3c8209e10dd12f7c052ac9d8f171dff174e2183f68720294b162e879166ae744883c74cbe56528c2908a4d17c6f245d9158491351cc1f11ecf79f8e9b828963e07c839eaf923c2db29d2c85d282326f83e9ccac334e3abf3e99b7e41811940426f97995494e2bae53f0ddd38ccd6dba26847723a77629f703c564c14da4880521e192976e09e7499a4"],"0x237ac394e3f6cbe7395fc7076a3b58036a0e185a519e41b35a87ba73679cc1bb"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x6d79536563726574446f63756d656e74"
}
```

***

### secretstore_signRawHash

This method is used to compute recoverrable ECDSA signatures, used in Secret Store: signatures of server key id and signatures of nodes set hash.
 **WARNING**: this method can be used to generate Ethereum-compatible signature of arbitrary hash, and should be enabled with caution.

#### Parameters

0. `Address` - address of account, which was used as requester in document [key retrieval session](Secret-Store#document-key-retrieval-session);
0. `String` - password for the passed account;
0. `Hash` - 256-bit hash to be signed (server key id or nodes set hash).

```js
params: [
  "0x00a329c0648769A73afAc7F9381E08FB43dBEA72",
  "",
  "0x0000000000000000000000000000000000000000000000000000000000000001"
]
```

#### Returns

- `Data` - Signed message with the given account's private key.

#### Example

Request
```bash
curl --data '{"method":"secretstore_signRawHash","params":["0x00a329c0648769A73afAc7F9381E08FB43dBEA72","","0x0000000000000000000000000000000000000000000000000000000000000001"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x7e11e1023d77135d6f395656fc66eeeffe7795824f1a5b2ffc78b1c5e8c84d5503de758f549a1372f797e124c0bd7b9f351bd8d6a8fa7cb4f173e8b431912aa300"
}
```

