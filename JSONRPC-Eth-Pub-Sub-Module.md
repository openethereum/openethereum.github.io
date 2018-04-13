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

An example notification received by subscribing to `newHeads` event:
```
{"jsonrpc":"2.0","method":"eth_subscription","params":{"subscription":"0x416d77337e24399d","result":{"difficulty":"0xd9263f42a87",<...>,
"uncles":[]}}}
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
curl --data '{"method":"eth_subscribe","params":["newHeads",{"fromBlock":"latest","toBlock":"latest"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x416d77337e24399d"
}
```

***

### eth_unsubscribe

Unsubscribes from a subscription.

#### Parameters

0. `String` - Subscription ID

```js
params: ["0x416d77337e24399d"]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
curl --data '{"method":"eth_unsubscribe","params":["0x416d77337e24399d"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```
