# Trace New Block inclusion

All numbering can be traced in code by searching `// t_nb x.y`
This is very rough tracing of new block inclusion for example transaction execution in virtual machine is just one point while it doesn't explain what is going on inside. Intention of this file is to make easier to find flow of of one feature in code. It covers stages from when block is received from network to when new block is executed, included and rebroadcasted to network.  

### 1. Receive and decode stage
* 1.0 decode rlp
* 1.1 check new block full difficulty (it can be found as second item in RLP) and calculate (full_dff-header_dif) and save it as peers current diff
* 1.2 if block number is older then 20 dont process it

### 2. Importing block into queue for validation
* 2.0 import block by client
* 2.1 check if header hash is known
* 2.2 check if parent is known
* 3.0 call VerificationQueue::import 3
* 2.4 If the block is okay
* 2.4.1 if the queue was empty we propagate the block in a `PriorityTask` to be rebrodcasted.
* 2.5 if block is not okay print error 

### 3. Check if same block is already in queue
VerificationQueue::import: 3
* 3.1 check if block is known
* 3.1.0 is currently processing
* 3.1.1 is marked as bad
* 3.1.2 is its parent marked as bad
* 3.2 call Kind::crate
* 4.0 called async

### 4. Basic block verification done on import
Phase1 verification Kind::Create  verify_block_basic
* 4.1 verify header parameters
* 4.2 verify header time
* 4.3 verify block integrity
* 4.4 Check block seal. It calls engine to verify block basic
* 4.5 Verify uncles
  - 4.5.1 verify uncle header
  - 4.5.2 call engine to verify uncle block basic
* 4.6 call engine.gas_limit_override (Used only by Aura)
* 4.7 for every transaction call engine.verify_transaction_basic
* 5.0 called async

### 5. Verification on solo block.
Phase 2 verification. Perform costly checks such as transaction signatures and block nonce for ethash.
* 5.1 engine.verify_block_unordered
* 5.2 engine.verify_block_unorderer for uncles
* 5.3 iterate over all transactions
  * 5.3.1 call verify_unordered. Check signatures and calculate address
  * 5.3.2 check if nonce is more then max nonce (EIP-168 and EIP169)
* 6.0 Called async.

### 6. Verification on parent->child, execute transaction and commit.
* 6.0 checks parent->child relationship, executes transactions and inserts block. (In OE call check_and_lock)
* 7.0 calls block to be enacted.
* 9.0 commit block
* 10.0 notify miner about new block
* 6.1 

### 7. Check and enact given block. 
* 7.1 check if block is older then last pruned block
* 7.2 Check if parent is in chain
* 7.3 Phase3 verification: verify block family. Done on parent and child headers.
  * 7.3.1 verify parent of current block. call engine.verify_block_family
  * 7.3.2 verify uncles
  * 7.3.3 verify all transactions and apply filters on them
* 7.4 Phase4 verification: verify block external. Calls engine.verify_block_external (Used only by AuRa)
* 7.5 Build last hashes. Get parent state from db. Get epoch_transition
* 8.0 Enact block
* 7.6 Strip receipts for blocks before validate_receipts_transition, if the expected receipts root header does not match. (i.e. allow inconsistency in receipts outcome before the transition block)
* 7.7 final verification. See if block that we enacted(executed) in 7.0 matches exactly with block that we received.


### 8. Block enacting. Execution of transactions.

For execution we create new OpenBlock, and execute tx while adding them to block)

* 8.0 enact block
* 8.1 Creates new OpenBlock
  * 8.1.1 Get parent StateDB
  * 8.1.2 engine.machine().populate_from_parent. It calculated child gas limits should be. and put it into new block to be checked against block that we want to include.
  * 8.1.3 calls engine.populate_from_parent mostly setting expected block difficulty.
  * 8.1.4 updating last hashes and the DAO fork, for ethash.
  * 8.1.5 engine.on_new_block (used only by AuRa)
* 8.2 transfer all field from current header to OpenBlock header that we created
* 8.3 execute transactions one by one
* 8.4 Push uncles to OpenBlock and check if we have more then max uncles
* 8.5 Turn this into a `LockedBlock`.
* 8.5.1 engine applies block rewards (Ethash and AuRa do.Clique is empty)
* 8.5.2 commit account changes from cache to tree
* 8.5.3 fill open block header with all other fields
* 

### 9. Commit block to database
* 9.1 engine.ancestry_actions. Gather all ancestry actions. (Used only by AuRa)
* 9.2 calcuate route between current and latest block.
* 9.3 engine.fork_choice. Check block total difficulty
* 9.4 propagate cached account to local cache
* 9.5 check epoch end signal, potentially generating a proof on the current state. Write transition into db.
* 9.6 push state from journaldb to database Transaction (It calls journal_under from JournalDB to insert state)
* 9.7 if there are finalized ancester, mark that chainge in block in db. (Used by AuRa)
* 9.9 insert traces (if they are enabled)
* 9.10 Propagate local cache into the global cache ...
* 9.11 Write Transaction to database (cached)
* 9.12 Apply pending insertion updates
* 9.12 commit changed to become current greatest by applying pending insertion updates (Important Sync point)
* 9.13 check epoch end. Related only to AuRa and it seems light client
* 9.14 update last hashes. They are build in step 7.5
* 9.15 prune ancient states until below the memory limit or only the minimum amount remain. (It calls mark_canonical from JournalDB to prune state)

### 10. Notify miner about new included block.

if there are imported blocks and there are no new blocks in pipiline that needs to be included, notify miner.

1. We ignore blocks that were `imported` unless resealing on new uncles is enabled.
2. We ignore blocks that are `invalid` because it doesn't have any meaning in terms of the transactions that are in those blocks

* 10.1 First update gas limit in transaction queue and minimal gas price.
* 10.2 Then import all transactions from retracted blocks.  (retracted means from side chain).
* 10.3 Reset `next_allowed_reseal` in case a block is imported. Even if min_period is high, we will always attempt to create new pending block.
* 10.4 if it is internal import, update sealing.
* 10.5 Make sure to cull transactions after we update sealing. Culls all stalled(included) transactions from the pool.
* 10.6 For service transaction checker update addresses to latest block


### 11. Notify rest of system about new block

This is done in not particular order. but for sake of numeration i will mark them with numbers.

* 11.1 Snapshot watcher. Check block number and trigger snapshot creation if needed.
* 11.2 Informant. Prints new block inclusion to console/log.
* 11.3 RPC. Notify subscribed header/logs about new block.
* 11.4 EthSync. called when block is imported to chain - propagates the blocks and updates transactions sent to peers
  * 11.4.1 propagate latest blocks to peers
    * 11.4.2 propagates new known hashes to all peers
    * 11.4.3 propagates latest block to a set of peers
  * 11.4.4 Distribute valid proposed blocks to subset of current peers. (if there is any proposed)
  * 11.4.5 Select random peer to re-broadcast transactions to.
* 11.5 SecretStore OnChainAclStorage.
* 11.6 SecretStore OnChainKeyServerSet.
* 11.7 SecretStore ServiceContractListener