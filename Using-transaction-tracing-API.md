* [trace_filter](#trace_filter)
* [trace_get](#trace_get)
* [trace_transaction](#trace_transaction)
* [trace_block](#trace_block)

***

 "traceAddress" field of all returned traces, gives the exact location in the call trace [index in root, index in first `CALL`, index in second `CALL`, ...] .

i.e. if the trace is:
```
A
  CALLs B
    CALLs G
  CALLs C
    CALLs G
```
then it should look something like:

`[ {A: []}, {B: [0]}, {G: [0, 0]}, {C: [1]}, {G: [1, 0]}]`

***

#### trace_filter

Returns traces matching given filter


##### Parameters

1. `Object` - The filter with fields:
  - `fromBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, `"latest"`, `"pending"` or `"earliest"`.
  - `toBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, `"latest"`, `"pending"` or `"earliest"`.
  - `fromAddress`: `Array` - (optional) Array of addresses which send the transaction (transaction must match at least one of them).
  - `toAddress`: `Array` - (optional) Array of addresses which received the transaction (transaction must match at least one of them).


##### Returns

`Array` - array of traces matching given filter.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"trace_filter","params":[{"fromBlock":"latest","toAddress":["0x0000000000000000000000000000000000000005"]}],"id":74}'

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": [{
    "action": {
      "call": {
        "from":"0x0000000000000000000000000000000000000004",
        "to":"0x0000000000000000000000000000000000000005",
        "value":"0x06",
        "gas":"0x07",
        "input":"0x1234"
      }
    },
    "result": {
      "call": {
        "gasUsed":"0x08",
        "output":"0x5678"
      }
    },
    "traceAddress":["0x2"],
    "subtraces":3,
    "transactionPosition":"0x0b",
    "transactionHash":"0x000000000000000000000000000000000000000000000000000000000000000c",
    "blockNumber":"0x0d",
    "blockHash":"0x000000000000000000000000000000000000000000000000000000000000000e"
  }]
}
```

***


#### trace_get

Returns trace at given position.


##### Parameters

1. `Hash` - Transaction hash.
2. `Array` - Trace position witing transaction.


##### Returns

`Object` - Trace.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"trace_get","params":["0x000000000000000000000000000000000000000000000000000000000000000c",["0x0a"]],"id":74}'

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": {
    "action": {
      "call": {
        "from":"0x0000000000000000000000000000000000000004",
        "to":"0x0000000000000000000000000000000000000005",
        "value":"0x06",
        "gas":"0x07",
        "input":"0x1234"
      }
    },
    "result": {
      "call": {
        "gasUsed":"0x08",
        "output":"0x5678"
      }
    },
    "traceAddress":["0x0a"],
    "subtraces":0,
    "transactionPosition":"0x0b",
    "transactionHash":"0x000000000000000000000000000000000000000000000000000000000000000c",
    "blockNumber":"0x0d",
    "blockHash":"0x000000000000000000000000000000000000000000000000000000000000000e"
  }
}
```

***

#### trace_transaction

Returns all traces of given transaction.


##### Parameters

1. `Hash` - Transaction hash.


##### Returns

`Array` - Returns all traces of given transaction.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"trace_transaction","params":["0x000000000000000000000000000000000000000000000000000000000000000c"],"id":74}'

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": [{
    "action": {
      "call": {
        "from":"0x0000000000000000000000000000000000000004",
        "to":"0x0000000000000000000000000000000000000005",
        "value":"0x06",
        "gas":"0x07",
        "input":"0x1234"
      }
    },
    "result": {
      "call": {
        "gasUsed":"0x08",
        "output":"0x5678"
      }
    },
    "traceAddress":[],
    "subtraces":0,
    "transactionPosition":"0x0b",
    "transactionHash":"0x000000000000000000000000000000000000000000000000000000000000000c",
    "blockNumber":"0x0d",
    "blockHash":"0x000000000000000000000000000000000000000000000000000000000000000e"
  }]
}
```

***

#### trace_block

Returns traces created at given block.


##### Parameters

1. `BlockNumber` - Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.


##### Returns

`Array` - array of block traces.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"trace_block","params":["latest"],"id":74}'

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": [{
    "action": {
      "call": {
        "from":"0x0000000000000000000000000000000000000004",
        "to":"0x0000000000000000000000000000000000000005",
        "value":"0x06",
        "gas":"0x07",
        "input":"0x1234"
      }
    },
    "result": {
      "call": {
        "gasUsed":"0x08",
        "output":"0x5678"
      }
    },
    "traceAddress":[],
    "subtraces":0,
    "transactionPosition":"0x00",
    "transactionHash":"0x000000000000000000000000000000000000000000000000000000000000000c",
    "blockNumber":"0x0d",
    "blockHash":"0x000000000000000000000000000000000000000000000000000000000000000e"
  }]
}
```

***
