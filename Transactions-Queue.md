---
title: Transaction Queue
---

Parity Ethereum client is listening to the network for transactions to include in blocks (if mining/validating) or to broadcast. These transactions emitted locally or externally are verified and ordered in a transaction queue. This document aims at describing the process of selecting or rejecting these transactions.

## Terminology
- `rejected` - means that a transaction was not added to the queue.
- `drop` - means that a transaction was added to the queue but got removed later on.

## Accepting conditions

Transactions received from the network must fulfil the following criteria in order to be added to the queue:
- the transaction's gas price is above the minimum set (configurable with the flag `--min-gas-price`) unless it is a [zero gas price transactions aka service transaction](Permissioning#gas-price) allowed by an on-chain managed contract.
- the amount of gas used by the transaction is lower than the current block gas limit.
- the amount of gas used by the transaction is below the maximum set (configurable with the flag `--tx-queue-gas`).
- the account sending the transaction has a sufficient balance to cover the fee costs (`gas * gas price`) on top of the `value` transfer.
- the transaction's signature is correct.
- the nonce is not in the *past*.
- the transaction's byte length of the RLP form does not exceed 300 * 1024 (or whatever is configured as `max_transaction_size` in the [chain specification](Chain-specification)).

Note that a transaction would be included in the queue if it fulfils the criteria mentioned above and:
- the nonce is `current`: equals to state nonce + number of already pending transactions.
or
- the nonce is `future`: there is a nonce gap, meaning that the transaction seems to have been received out of order, parity will, therefore, keep it, anticipating that transactions with lower nonces will fill the gap (gap = `expected nonce` vs `transaction nonce`). A `future` transactions will not be propagated nor will it get included in the pending block as it would be invalid.

## Dropping conditions
Once a transaction has been added to the queue, it might get dropped if:
- any pre-verified condition has changed (nonce and balance change in the meantime)
- the queue length limit is reached (configurable with the flag `--tx-queue-size`)
- the per-sender max number of transactions is reached (configurable with the flag `--tx-queue-per-sender`)
- the memory limit was reached (configurable with the flag `--tx-queue-mem-limit`)
- a different transaction with the same nonce was signed by the same sender, and its gas price is at least 12.5% higher than the previous one. This new transaction would replace the transaction that is already in the pool.

Note that the last 3 conditions require additionally that another transaction with a higher overall score (i.e. `gas price`), potentially from a different sender, fulfils the requirement to enter the pool while it's full.

## Dropping logic
Given the following transactions in a queue from a *same non local sender account*:

`[TX(nonce=1)| TX(nonce=2) | Tx(nonce=3) | Tx(nonce=4)]`

Where `nonce=1` is in the past, `nonce=2` is current, `nonce=3` and `nonce=4` are in the future.
`nonce=1` can be dropped without discussion. The transaction with `nonce=5` would be the "lowest rated" transaction for this sender as it is far in the future. The system would then compare this transaction with the other transactions in the queue from the other senders. The decision to drop a transaction is taken based on the transaction queue strategy (`--tx-queue-strategy`), which is  `gas_price` per default. If the transaction `nonce=5` has the lowest gas price among the "lowest rated" transactions from the queue, it will be dropped.

## Exceptions

### Local transactions
Local transactions are transactions that have been created or submitted to the local node. Such transactions have a priority boost in the queue - they will get propagated first and included in the pending block first.
Local transactions can be submitted via two RPC methods: `eth_sendTransaction` and `eth_sendRawTransaction`. For the former the node has to manage the private key (the sender address), for the latter the transaction is going to be marked as local no matter the sender.

### Configuration flags
- `--tx-queue-no-unfamiliar-locals`: This flag disables treating all transactions received using `eth_sendRawTransaction` as local. Only transactions that are signed by one of the local accounts (private key) will get marked as local, others will be treated in the same way as if they were received over the network.
The flag is recommended in case your RPC is exposed for external (potentially untrusted) clients.

- `--tx-queue-no-early-reject`: Disables early-rejection of the transactions in case the queue is at it's limits. By default if the pool is full we omit most of the (heavy) verification logic for the transactions and reject transactions early only by looking at their gas price. As a consequence we are not able to identify transactions received from the network as the ones that should be prioritized (local), because the node is managing the private key for them. If that behavior is undesired (i.e. you sign/submit local transactions on node A, then want them to be propagated to node B and prioritized there) you can use the flag to disable early rejection.

#### Recap
The table below shows when transaction is considered as local:

|                                 	|  Received via RPC `eth_sendTransaction`  	|  Received via RPC `eth_sendRawTransaction` 	|   Received over the network   and sender is local account  	|
|:-------------------------------:	|:----------------------------------------:	|:------------------------------------------:	|:----------------------------------------------------------:	|
| no flag                         	|                    YES                   	|                     YES                    	|                             NO*                            	|
| `tx-queue-no-unfamiliar-locals` 	|                    YES                   	|                    NO**                    	|                             NO*                            	|
| `tx-queue-no-early-reject`        	|                    YES                   	|                     YES                    	|                             YES                            	|
| both                            	|                    YES                   	|                    NO**                    	|                             YES                            	|

```
 * YES if the pool is not full
** YES if the sender is local account
```

## RPC APIs

The RPC API will only show the external transactions that are in the queue. They will never return rejected transactions. 
- [`parity_localTransactions`](JSONRPC-parity-module#parity_localtransactions) will show up to 25 local transactions, be they already mined, pending or [dropped](#dropping-conditions). This RPC method is used in the top section of the Parity UI TxQueueViewer Dapp.
- [`parity_pendingTransactions`](JSONRPC-parity-module#parity_pendingtransactions) will show the transactions added to the queue that will be processed soon (and do not fulfil any of the [droping conditions](#dropping-conditions)). This RPC method is used in the bottom section of the Parity UI TxQueueViewer Dapp.
- [`parity_allTransactions`](/JSONRPC-parity-module#parity_alltransactions) will show the transactions added to the queue that will be processed soon as well as the ones that might get dropped.
- [`parity_futureTransactions`](JSONRPC-parity-module#parity_futuretransactions) is deprecated in favor of `parity_allTransactions`. It can be computed by subtracting `parity_pendingTransactions` to `parity_allTransactions`.
