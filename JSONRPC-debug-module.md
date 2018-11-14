---
title: The `debug` Module
---

The `debug` API gives you access to several non-standard RPC methods, which will allow you to inspect, debug and set certain debugging flags during runtime.

## JSON-RPC methods

- [debug_getBadBlocks](#debug_getbadblocks)

## JSON-RPC API Reference

### debug_getBadBlocks

Returns a list of the last "bad blocks" that the client has seen on the network.

#### Parameters

None

#### Returns

- `Array` - List of "bad blocks" seen on the network.

#### Example

Request
```bash
curl --data '{"method":"debug_getBadBlocks","params":[],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [{
    "author": "0x0000000000000000000000000000000000000000",
    "difficulty": "0x0",
    "extraData": "0x",
    "gasLimit": "0x0",
    "gasUsed": "0x0",
    "hash": "0x27bfb37e507ce90da141307204b1c6ba24194380613590ac50ca4b1d7198ff65",
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "miner": "0x0000000000000000000000000000000000000000",
    "number": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "reason": "Invalid block",
    "receiptsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "rlp": "0x010203",
    "sealFields": [],
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "size": "0x3",
    "stateRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "timestamp": "0x0",
    "totalDifficulty": null,
    "transactions": [],
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "uncles": []
  }]
}
```

