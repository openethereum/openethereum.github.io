---
title: The `eth_pubsub` Module
---

## JSON-RPC methods

- [eth_subscribe](#eth_subscribe)
- [eth_unsubscribe](#eth_unsubscribe)

## JSON-RPC API Reference

### eth_subscribe


Starts a subscription (on WebSockets / IPC / TCP transports) to a particular event. For every event that
matches the subscription a JSON-RPC notification with event details and subscription ID will be sent to a client.

Below examples use `wscat`, a simple command line WebSockets client. Find out how to install and use it by visiting [wscat GitHub repository](https://github.com/websockets/wscat).

An example notification received by subscribing to `newHeads` event:
```js
{
    "jsonrpc": "2.0",
    "method": "eth_subscription",
    "params": {
        "result": {
            "author": "0xea674fdde714fd979de3edf0f56aa9716b898ec8",
            "difficulty": "0xb33e1bf54955f",
            "extraData": "0x65746865726d696e652d6177732d61736961312d32",
            "gasLimit": "0x7a121d",
            "gasUsed": "0x79c602",
            "hash": "0x86436b61e0f54c404fff92909d1d2f881a476f0982ff6fc5b3141c90226d6ad1",
            "logsBloom": "0x5a4380886d4448b03805022082080d920020c0010c0d004010c70d0d80a8d56a187cb81800004004099032e042426b2007c2c10244300ab822b84204486008d010188c44808c005f00d2c9eb620c1081601861039129202884a14200410606000420002066b240500042a44002000d18008e021a0a0d04800008201f10002ca52029c104202310405591e500008a5008812290101083101c00428005002c03130a1e0402407260180644140618c08080832c5d89000080914228844180392c020c1058c3040c21220510010023202c040c4082c442e818022145a02a4208200101d48955099089bec4c08080c006831440200300000108cf8a00440520800390",
            "miner": "0xea674fdde714fd979de3edf0f56aa9716b898ec8",
            "mixHash": "0x084b405b7d0ff080c1fbf23ab27a4431aaf9c986c1bd959695993d957eaf81f7",
            "nonce": "0xa527ccd80dd16040",
            "number": "0x5682c7",
            "parentHash": "0xc8e5713f1d7136f02062e840f825f033af00e2adfac1b6dd7c71d146fcd60d4a",
            "receiptsRoot": "0x4852c35a8adb6720e380d8c79a2bb9050fa131b0b53194f56a61982fe6db1059",
            "sealFields": ["0x084b405b7d0ff080c1fbf23ab27a4431aaf9c986c1bd959695993d957eaf81f7", "0xa527ccd80dd16040"],
            "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
            "size": "0x219",
            "stateRoot": "0xf4fee9cf65ed10b1020810c305023dc51e4db4dbdcc9c8f3ae2b6e9334c07087",
            "timestamp": "0x5b06e6c8",
            "transactionsRoot": "0xbc3af100baf8e088f703a1d357375c34e1f0b69176550e10240cc71e702b09ab"
        },
        "subscription": "0xb53c4832f1dca4a5"
    }
}
```

You can unsubscribe using `eth_unsubscribe` RPC method. Subscriptions are also tied to a transport
connection, disconnecting causes all subscriptions to be canceled.


#### Parameters

0. `String` - Subscription type: one of `newHeads`, `logs`
0. `Object` - 
Subscription type-specific parameters. It must be left empty for
`newHeads` and must contain filter object for `logs`.


```js
params: [
  "logs",
  {
    "fromBlock": "latest",
    "toBlock": "latest"
  }
]
```

#### Returns

- `String` - Assigned subscription ID

#### Example

Request
```bash
wscat -c localhost:8546
>{"method":"eth_subscribe","params":["logs",{"fromBlock":"latest","toBlock":"latest"}],"id":1,"jsonrpc":"2.0"}
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xb53c4832f1dca4a5"
}
```

***

### eth_unsubscribe

Unsubscribes from a subscription.

#### Parameters

0. `String` - Subscription ID

```js
params: ["0xb53c4832f1dca4a5un"]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
wscat -c localhost:8546
>{"method":"eth_unsubscribe","params":["0xb53c4832f1dca4a5un"],"id":1,"jsonrpc":"2.0"}
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

