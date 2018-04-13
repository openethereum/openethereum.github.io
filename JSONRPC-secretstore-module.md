---
title: Secret Store RPC methods
---
Parity has separate RPC API set - `secretstore`, which:
- is available in default Parity client (i.e. does not requires Parity to be built with `--features secretstore`);
- is considered unsafe and must be enabled separately (by passing `secretstore`, `all` or `safe` as an `--jsonrpc-apis` argument value);
- contains dangerous methods and must be enabled with caution.

## secretstore_encrypt
This method can be used after running [document key retrieval session](Secret-Store.md#document-key-retrieval-session) or [server and document key generation session](Secret-Store.md#server-and-document-key-generation-session). You can use it to encrypt small document:
```bash
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_encrypt", "params": ["0x00a329c0648769A73afAc7F9381E08FB43dBEA72", "password", "0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4", "0xdeadbeef"], "id":1 }' -H 'content-type: application/json' http://127.0.0.1:8545/
```

Here:
- `0x00a329c0648769A73afAc7F9381E08FB43dBEA72`: is the address of account, which was used as requester in [document key retrieval session](Secret-Store.md#document-key-retrieval-session);
- `password`: is the password for the passed account;
- `0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4`: is the data, returned by [document key retrieval session](Secret-Store.md#document-key-retrieval-session);
- `0xdeadbeef`: is the hex-encoded document data.

## secretstore_decrypt
This method can be used to decrypt document, encrypted by [`secretstore_encrypt`](#secretstore_encrypt) method before:
```bash
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_decrypt", "params": ["0x00a329c0648769A73afAc7F9381E08FB43dBEA72", "password", "0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4", "0x1bf50b05b82952801f9591d91c52098baa0c6940"], "id":1 }' -H 'content-type: application/json' http://127.0.0.1:8545/
```

Here:
- `0x00a329c0648769A73afAc7F9381E08FB43dBEA72`: is the address of account, which was used as requester in [document key retrieval session](Secret-Store.md#document-key-retrieval-session);
- `password`: is the password for the passed account;
- `0x049b05477a02b3197d568e1fa6fbfa4152316eea499d4f6c1c72f215246f87cf910bbd0951067466e8eb8d05437686ab6cf15caaffc9388a5b6c1cfc65eae556c949bae498a36c3bd630b47d852593f9ff9e0cac62e611afc0ae620ccf74b7e3925f2becb64a3afa7d74c33b8761d69af052dd8363d9dd28516ab80521399774737ec98d04bd118325fc242098e71fd9641430b7bde42f6845b86d61fa5fc6b77920c8eca923da955cd136cdc79467bba4`: is the data, returned by [document key retrieval session](Secret-Store.md#document-key-retrieval-session);
- `0x1bf50b05b82952801f9591d91c52098baa0c6940`: is the encrypted document data (result of previous [`secretstore_encrypt`](#secretstore_encrypt) call).

## secretstore_shadowDecrypt
This method can be used to decrypt document, encrypted by [`secretstore_encrypt`](#secretstore_encrypt) method before:
```bash
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_shadowDecrypt", "params": ["0x00a329c0648769A73afAc7F9381E08FB43dBEA72", "password", "0x843645726384530ffb0c52f175278143b5a93959af7864460f5a4fec9afd1450cfb8aef63dec90657f43f55b13e0a73c7524d4e9a13c051b4e5f1e53f39ecd91", "0x07230e34ebfe41337d3ed53b186b3861751f2401ee74b988bba55694e2a6f60c757677e194be2e53c3523cc8548694e636e6acb35c4e8fdc5e29d28679b9b2f3", ["0x049ce50bbadb6352574f2c59742f78df83333975cbd5cbb151c6e8628749a33dc1fa93bb6dffae5994e3eb98ae859ed55ee82937538e6adb054d780d1e89ff140f121529eeadb1161562af9d3342db0008919ca280a064305e5a4e518e93279de7a9396fe5136a9658e337e8e276221248c381c5384cd1ad28e5921f46ff058d5fbcf8a388fc881d0dd29421c218d51761"], "0x1bf50b05b82952801f9591d91c52098baa0c6940"], "id":1 }' -H 'content-type: application/json' http://127.0.0.1:8545/
```

Here:
- `0x00a329c0648769A73afAc7F9381E08FB43dBEA72`: is the address of account, which was used as requester in [document key shadow retrieval session](Secret-Store.md#document-key-shadow-retrieval-session);
- `password`: is the password for the passed account;
- `0x843645726384530ffb0c52f175278143b5a93959af7864460f5a4fec9afd1450cfb8aef63dec90657f43f55b13e0a73c7524d4e9a13c051b4e5f1e53f39ecd91`: is the value of `decrypted_secret` field from [document key shadow retrieval session](Secret-Store.md#document-key-shadow-retrieval-session) result;
- `0x07230e34ebfe41337d3ed53b186b3861751f2401ee74b988bba55694e2a6f60c757677e194be2e53c3523cc8548694e636e6acb35c4e8fdc5e29d28679b9b2f3`: is the value of `common_point` field from [document key shadow retrieval session](Secret-Store.md#document-key-shadow-retrieval-session) result;
- `["0x049ce50bbadb6352574f2c59742f78df83333975cbd5cbb151c6e8628749a33dc1fa93bb6dffae5994e3eb98ae859ed55ee82937538e6adb054d780d1e89ff140f121529eeadb1161562af9d3342db0008919ca280a064305e5a4e518e93279de7a9396fe5136a9658e337e8e276221248c381c5384cd1ad28e5921f46ff058d5fbcf8a388fc881d0dd29421c218d51761"]`: is the value of `decrypt_shadows` field from [document key shadow retrieval session](Secret-Store.md#document-key-shadow-retrieval-session) result;
- `0x1bf50b05b82952801f9591d91c52098baa0c6940`: is the encrypted document data (result of previous [`secretstore_encrypt`](#secretstore_encrypt) call).

## secretstore_serversSetHash
Computes the hash of nodes ids, required to compute nodes set signature for manual [nodes set change session](Secret-Store-Configuration.md#changing-servers-set-configuration):
```bash
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_serversSetHash", "params": [["0x843645726384530ffb0c52f175278143b5a93959af7864460f5a4fec9afd1450cfb8aef63dec90657f43f55b13e0a73c7524d4e9a13c051b4e5f1e53f39ecd91", "0x07230e34ebfe41337d3ed53b186b3861751f2401ee74b988bba55694e2a6f60c757677e194be2e53c3523cc8548694e636e6acb35c4e8fdc5e29d28679b9b2f3"]], "id":1 }' -H 'content-type: application/json' http://127.0.0.1:8545/
```

Here:
- `["0x843645726384530ffb0c52f175278143b5a93959af7864460f5a4fec9afd1450cfb8aef63dec90657f43f55b13e0a73c7524d4e9a13c051b4e5f1e53f39ecd91", "0x07230e34ebfe41337d3ed53b186b3861751f2401ee74b988bba55694e2a6f60c757677e194be2e53c3523cc8548694e636e6acb35c4e8fdc5e29d28679b9b2f3"]`: is the set of nodes ids.

## secretstore_signRawHash
This method is used to compute recoverrable ECDSA signatures, used in Secret Store: signatures of server key id and signatures of nodes set hash. Usage:
```bash
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_signRawHash", "params": [["0x00a329c0648769A73afAc7F9381E08FB43dBEA72", "password", "0x0000000000000000000000000000000000000000000000000000000000000001"]], "id":1 }' -H 'content-type: application/json' http://127.0.0.1:8545/
```

Here:
- `0x00a329c0648769A73afAc7F9381E08FB43dBEA72`: is the address to be used for signature generation (requester);
- `password`: is the password for the passed account;
- `0x0000000000000000000000000000000000000000000000000000000000000001`: is the 256-bit hash to be signed (server key id or nodes set hash).

**WARNING**: this method can be used to generate Ethereum-compatible signature of arbitrary hash, and should be enabled with caution.

## secretstore_generateDocumentKey
This method is used to securely generate document key, so that it remains unknown to all key servers. After generating server key using [server key generation session](Secret-Store.md#server-key-generation-session), you should pass returned server key to this method and it will gave you all required data to encrypt your document by using [secretstore_encrypt](#secretstore_encrypt) (`encrypted_key`) and to safely store document key in the Secret Store by using [document key storing session](Secret-Store.md#document-key-storing-session) (`common_point` and `encrypted_point`). Usage:
```bash
curl --data-binary '{"jsonrpc": "2.0", "method": "secretstore_generateDocumentKey", "params": ["0x00a329c0648769A73afAc7F9381E08FB43dBEA72", "password","0x2eabc29df5b62c75011bf1016237212b6305f8bae0f979b7b92250cfea06c20fe1689fc6d98964be64532598e3db7fc5712ad24b95e161f95bcfe1c6f859da3a"], "id":1 }' -H 'content-type: application/json' http://127.0.0.1:8545/
```

Here:
- `0x00a329c0648769A73afAc7F9381E08FB43dBEA72`: is the address to be used for signature generation (requester);
- `password`: is the password for the passed account;
- `0x2eabc29df5b62c75011bf1016237212b6305f8bae0f979b7b92250cfea06c20fe1689fc6d98964be64532598e3db7fc5712ad24b95e161f95bcfe1c6f859da3a` is the server key.