# Aura: Authority Round

Aura _(Authority Round)_ is one of the Blockchain consensus algorithms available in Parity.

Parameters:
  - _n_, the number of nodes
  - _f_, the number of faulty nodes
  - _t_, the step duration in seconds

**Description**:

Time is divided into discrete steps of duration `t`, determined by `UNIX time / t`. At each step `s`, a _primary_ will be assigned. Only the primary at a step may issue a block. It is misbehavior to produce more than one block per step or to produce a block out of turn.

The primary for a step `s` is the node with index `s % n`.

The protocol contains a chain scoring rule `SCORE(C)`

On each step, each honest node will propagate the chain with the highest score it knows about to all other nodes. Honest primaries will only issue blocks on top of the best chain they are aware of during their turn.

**Finality**

Under the assumption of a synchronous network which propagates messages within the step duration `t`,

`Let SIG_SET(Blocks) be the set of all authors of the blocks in Blocks`

If there is a valid chain C ending with `C[K..]`, where `|SIG_SET(C[K..])| > n/2`,
then `C[K]` and all of its ancestors are finalized.

This definition of finality stems from a simple majority vote. In this setting, `2f + 1 <= n`, so the faulty nodes cannot finalize a block all on their own.

**Node Configuration:**

This consensus requires a [`ValidatorSet`](Validator-Set.md) to be specified, which determines the list of `n_v` blockchain addresses at each height `h` which participate in the consensus.

A node can represent a validator when it is ran with `--engine-signer VALIDATOR-ADDRESS`.

The consensus can be run with `--force-sealing` which ensures that blocks are produced even if there are no transactions. This is necessary for blocks to reach finality in a timely fashion.

**Wishlist:**

- Instead of creating empty blocks, broadcast signed `EMPTY(Step)` message. Include in seal when creating a block with transactions or an empty block once there are more than some amount of new signed step messages.
- Apply step backoff after skipping primaries, not before. Exponential backoff may allow for weakly synchronous network without failure.
- Faster finality by broadcasting signed `GOOD(Hash)` messages where Hash is a block hash of a block with number multiple of some epoch length. `GOOD` messages can be included in the seal.
