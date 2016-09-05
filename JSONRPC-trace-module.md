# The `trace` Module

The trace module is for getting a deeper insight into transaction processing. It includes two sets of calls; the transaction trace filtering API and the ad-hoc tracing API.

## The Transaction-Trace Filtering API

These APIs allow you to get a full *externality* trace on any transaction executed throughout the Parity chain. Unlike the log filtering API, you are able to search and filter based only upon address information. Information returned includes the execution of all `CREATE`s, `SUICIDE`s and all variants of `CALL` together with input data, output data, gas usage, amount transferred and the success status of each individual action.

In order to use these API Parity must be fully synced with flags `$ parity --tracing on`.

***

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
    "type": "call",
    "action": {
      "from":"0x0000000000000000000000000000000000000004",
      "to":"0x0000000000000000000000000000000000000005",
      "value":"0x06",
      "gas":"0x07",
      "input":"0x1234",
      "callType":"call"
    },
    "result": {
      "gasUsed":"0x08",
      "output":"0x5678"
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
    "type": "call",
    "action": {
      "from":"0x0000000000000000000000000000000000000004",
      "to":"0x0000000000000000000000000000000000000005",
      "value":"0x06",
      "gas":"0x07",
      "input":"0x1234",
      "callType":"call"
    },
    "result": {
      "gasUsed":"0x08",
      "output":"0x5678"
    },
    "traceAddress":["0x2"],
    "subtraces":3,
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
    "type": "call",
    "action": {
      "from":"0x0000000000000000000000000000000000000004",
      "to":"0x0000000000000000000000000000000000000005",
      "value":"0x06",
      "gas":"0x07",
      "input":"0x1234",
      "callType":"call"
    },
    "result": {
      "gasUsed":"0x08",
      "output":"0x5678"
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
    "type": "call",
    "action": {
      "from":"0x0000000000000000000000000000000000000004",
      "to":"0x0000000000000000000000000000000000000005",
      "value":"0x06",
      "gas":"0x07",
      "input":"0x1234",
      "callType":"call"
    },
    "result": {
      "gasUsed":"0x08",
      "output":"0x5678"
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

##### Fields description

- `type` - *String* - one of: `call`, `create`, `suicide`
- `action` - *Object*
- for `call` it's `Object` with the following fields:
    - `from` - *Address* - sender
    - `to` - *Address* - receiver
    - `value` - *Integer* - call value
    - `gas` - *Integer* - call gas
    - `input` - *Bytes* - transaction input
    - `callType`
  - for `create` it's `Object` with the following fields:
    - `from` - *Address* - sender
    - `value` - *Integer* - value
    - `gas` - *Integer* - gas
    - `init` - *Bytes* - contract init code
  - for `suicide` it's `Object` with the following fields:
    - `address` - *Address* - address of the contract which commited suicide
    - `refundAddress` - *Address* - address which received funds of suicided contract
    - `balance` - *Integer* - balance of the contract
- `result` - *Object* - exists only if action is successfull
  - for `call` it's `Object` with the following fields:
    - `gasUsed` - *Integer*
    - `output` - *Bytes*
  - for `create` it's `Object` with the following fields:
    - `gasUsed` - *Integer*
    - `code` - *Bytes* - new contract's code
    - `address` - *Address* - new contract's address
  - for `suicide` it's `null`
- `error` - *String* - exists only if action has failed
- `traceAddress` - *Array* - trace address within transaction
- `subtraces` - *Integer* - number of subtraces
- `transactionPosition` - *Integer* - transaction position within block
- `transactionHash` - *H256* - transaction hash
- `blockNumber` - *Integer* - block in which transaction was mined
- `blockHash` - *H256* - block hash

***


## The Ad-hoc Tracing API

The ad-hoc tracing API allows you to perform a number of different diagnostics on calls or transactions, either historical ones from the chain or hypothetical ones not yet mined. The diagnostics include:

- `trace` **Transaction trace**. An equivalent trace to that in the previous section.
- `vmTrace` **Virtual Machine execution trace**. Provides a full trace of the VM's state throughout the execution of the transaction, including for any subcalls.
- `stateDiff` **State difference**. Provides information detailing all altered portions of the Ethereum state made due to the execution of the transaction.

There are three means of providing a transaction to execute; either providing the same information as when making a call using `eth_call` (see `trace_call`), through providing raw, signed, transaction data as when using `eth_sendRawTransaction` (see `trace_rawTransaction`) or simply a transaction hash for a previously mined transaction (see `trace_replayTransaction`). In the latter case, your node must be in archive mode or the transaction should be within the most recent 1000 blocks.

- `trace_call` `()` *returns* ``
- `trace_rawTransaction` `()` *returns* ``
- `trace_replayTransaction` `()` *returns* ``
