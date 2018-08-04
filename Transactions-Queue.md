---
title: Transaction Queue
---

Parity client is listening to the network for transactions in order to include them in a block (if mining/validating) or to broadcast them. These transactions emitted locally or externally are verified and ordered in a transaction queue. This document aims at describing the process of selection and rejection of these transactions.

## Queue logic

### Terminology
- `rejected` - means that transaction will not be added to the queue.
- `drop` - means that transaction was added to the queue but got removed.

### Accepting conditions

As a transaction is received from the network, a verification is performed before adding it to the queue. The following criteria are checked:
- the transaction's gas price is above the minimum set (configurable with the flag `--min-gas-price`) unless it is a [zero gas price transactions aka service transaction](Permissioning#gas-price) allowed by an on-chain managed contract.
- the amount of gas used by the transaction is lower than the current block gas limit.
- the amount of gas used by the transaction is below the maximum set (configurable with the flag `--tx-queue-gas`).
- the account sending the transaction has a sufficient balance to cover the fee costs (`gas * gas price`) on top of the `value` transfer.
- the transaction's signature is correct.
- the nonce is not in the *past*.
- the transaction's byte length of the RLP form does not exceed 300 * 1024 (or whatever is configured as `max_transaction_size` in the [chain specification](Chain-specification)).

Note that a transaction would be included in the queue if it fulfills the above-mentioned criteria and:
- the nonce is `current`: equals to state nonce + number of already pending transactions.
or
- the nonce is `future`: there is a nonce gap, meaning that the transaction seems to have been received out of order, parity will, therefore, keep it, anticipating that transactions with lower nonces will fill the gap (gap = `expected nonce` vs `transaction nonce`). A `future` transactions will not be propagated nor will it get included in the pending block as it would be invalid.

### Dropping conditions
Once a transaction has been added to the queue, it might get dropped if:
- any pre-verified condition has changed (nonce and balance change in the meantime)
- the queue length limit is reached (configurable with the flag `--tx-queue-size`)
- the per-sender max number of transactions is reached (configurable with the flag `--tx-queue-per-sender`)
- the memory limit was reached (configurable with the flag `--tx-queue-mem-limit`)
- a different transaction with the same nonce was signed by the same sender, and its gas price is at least 12.5% higher than the previous one. This new transaction would replace the transaction that is already in the pool.

Note that the last 3 conditions require additionally that another transaction with a higher overall score (i.e. `gas price`), potentially from a different sender, fulfills the requirement to enter the pool while it's full.

Note also that local transactions will never get dropped to ease debugging.

### Dropping logic
Given the following transactions in a queue from a *same sender*:

`[TX(nonce=1)| TX(nonce=2) | Tx(nonce=3) | Tx(nonce=4)]`

Where `nonce=1` is in the past, `nonce=2` is current, `nonce=3` and `nonce=4` are in the future.
`nonce=1` can be dropped without discussion. The transaction with `nonce=5` would be the "lowest rated" transaction for this sender as it is far in the future. The system would then compare this transaction with the other transactions in the queue from the other senders. The decision to drop a transaction is taken based on the transaction queue strategy (`--tx-queue-strategy`), which is  `gas_price` per default. If the transaction `nonce=5` has the lowest gas price among the "lowest rated" transactions from the queue, it will be dropped.

## RPC APIs

The RPC API will only show the external transactions that are in the queue. They will never return rejected transactions. 
- [`parity_localTransactions`](JSONRPC-parity-module#parity_localtransactions) will show up to 25 local transactions, be they already mined, pending or [dropped](#dropping-conditions). This RPC method is used in the top section of the Parity UI TxQueueViewer Dapp.
- [`parity_pendingTransactions`](JSONRPC-parity-module#parity_pendingtransactions) will show the transactions added to the queue that will be processed soon (and do not fulfill any of the [droping conditions](#dropping-conditions)). This RPC method is used in the bottom section of the Parity UI TxQueueViewer Dapp.
- [`parity_allTransactions`](/JSONRPC-parity-module#parity_alltransactions) will show the transactions added to the queue that will be processed soon as well as the ones that might get dropped.
- [`parity_futureTransactions`](JSONRPC-parity-module#parity_futuretransactions) is deprecated in favor of `parity_allTransactions`. It can be computed by subtracting `parity_pendingTransactions` to `parity_allTransactions`.
