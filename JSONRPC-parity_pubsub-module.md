---
title: The `parity_pubsub` Module
---

## JSON-RPC methods

- [parity_subscribe](#parity_subscribe)
- [parity_unsubscribe](#parity_unsubscribe)

## JSON-RPC API Reference

### parity_subscribe


Starts a subscription (on WebSockets / IPC / TCP transports) to results of calling some other RPC method.
For every change in returned value of that RPC call a JSON-RPC notification with result and subscription ID will be sent to a client.


Below examples use `wscat`, a simple command line WebSockets client. Find out how to install and use it by visiting [wscat GitHub repository](https://github.com/websockets/wscat).

An example notification received by subscribing to `eth_getBalance` RPC method:
```
{"jsonrpc":"2.0","method":"parity_subscription","params":{"subscription":"0x416d77337e24399d","result":["0xcd2a3d9f938e13cd947ec05abc7fe734df8dd826"]}}
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
>{"method":"parity_subscribe","params":["eth_getBalance",["0xcd2a3d9f938e13cd947ec05abc7fe734df8dd826","latest"]],"id":1,"jsonrpc":"2.0"}
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

### parity_unsubscribe

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
wscat -c localhost:8546
>{"method":"parity_unsubscribe","params":["0x416d77337e24399d"],"id":1,"jsonrpc":"2.0"}
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

