---
title: The `parity_pubsub` Module
---

## JSON-RPC methods

- [parity_subscribe](#parity_subscribe)
- [parity_unsubscribe](#parity_unsubscribe)

## JSON-RPC API Reference

Below examples use `wscat`, a simple command line WebSocket client. Find out how to install and use it by visiting [wscat GitHub repository](https://github.com/websockets/wscat).

### parity_subscribe


Starts a subscription (on WebSockets / IPC / TCP transports) to results of calling some other RPC method.
For every change in returned value of that RPC call a JSON-RPC notification with result and subscription ID will be sent to a client.

An example notification received by subscribing to `eth_getBalance` RPC method:
```
{
    "jsonrpc": "2.0",
    "method": "parity_subscription",
    "params": {
        "result": "0x168e0074d33c31f18",
        "subscription": "0x070fa1c4d1b3fd81"
    }
}
```

You can unsubscribe using `parity_unsubscribe` RPC method. Subscriptions are also tied to a transport
connection, disconnecting causes all subscriptions to be canceled.


#### Parameters

0. `String` - RPC method name
0. `Array` - Parameters passed to RPC method. (Optional, defaults to no parameters)

```js
params: [
  "eth_getBalance",
  [
    "0xcd2a3d9f938e13cd947ec05abc7fe734df8dd826",
    "latest"
  ]
]
```

#### Returns

- `String` - Assigned subscription ID

#### Example

Request
```bash
wscat -c localhost:8546
> {"method":"parity_subscribe","params":["eth_getBalance",["0x004702bdcC3C7dbFfd943136107E70B827028600","latest"]],"id":1,"jsonrpc":"2.0"}
```

Response
```js
{
    "jsonrpc": "2.0",
    "result": "0x070fa1c4d1b3fd81",
    "id": 1
}
```

***

### parity_unsubscribe

Unsubscribes from a subscription.

#### Parameters

0. `String` - Subscription ID

```js
params: ["0x070fa1c4d1b3fd81"]
```

#### Returns

- `Boolean` - whether the call was successful

#### Example

Request
```bash
wscat -c localhost:8546
> {"method":"parity_unsubscribe","params":["0x070fa1c4d1b3fd81"],"id":1,"jsonrpc":"2.0"}
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```
