---
title: Aura - Authority Round
libs:
  mathjax: true
---

Aura _(Authority Round)_ is one of the Blockchain consensus algorithms available in Parity.

### Parameters
  - $$n$$, the number of nodes
  - $$f$$, the number of faulty nodes
  - $$t$$, the step duration in seconds

### Description

Time is divided into discrete steps of duration $$t$$, determined by $$\frac{UNIX\ time}{t}$$. At each step $$s$$, a _primary_ will be assigned. Only the primary at a step may issue a block. It is misbehavior to produce more than one block per step or to produce a block out of turn.

The primary for a step $$s$$ is the node with index: $$s \bmod n$$.

The protocol contains a chain scoring rule $$SCORE(C)$$ for a given chain $$C$$.

On each step, each honest node will propagate the chain with the highest score it knows about to all other nodes. Honest primaries will only issue blocks on top of the best chain they are aware of during their turn.

### Finality

Under the assumption of a synchronous network which propagates messages within the step duration $$t$$, let $$SIG\_SET(B)$$ be the set of signatures from all authors in the set of blocks $$B$$:

$$SIG\_SET(B) = \left\{ a | \exists{b} \in B : AUTHOR(b) = a \right\}$$

If there is a valid chain $$C$$ ending with $$C[K..]$$, where $$\left\vert{SIG\_SET(C[K..])}\right\vert > \frac{n}{2}$$, then $$C[K]$$ and
all of its ancestors are finalized.

This definition of finality stems from a simple majority vote. In this setting, $$2f + 1 \leq n$$, so the faulty nodes cannot finalize a block all on their own.

#### Empty steps

In order to reach finality in a timely fashion it is necessary for the nodes to keep sealing blocks even when there are no transactions. 
To reduce blockchain bloat while still maintaining the same finality guarantees the nodes can sign and broadcast an `EmptyStep(step, parent_hash)` message instead of producing an empty block. All of the nodes accumulate the broadcasted empty step messages and they are included and rewarded in the next non-empty block.

The empty step messages included in blocks are also taken into account for finality.

To enable empty step messages set the `emptyStepsTransition` to enable it at the given block number. You can also specify a maximum number of empty steps with `maximumEmptySteps` in your chain spec.

### Node Configuration

This consensus requires a [`ValidatorSet`](Validator-Set.md) to be specified, which determines the list of $$n$$ blockchain addresses at each height $$h$$ which participate in the consensus.

A node can represent a validator when it is run with `--engine-signer VALIDATOR-ADDRESS`.

The consensus can be run with `--force-sealing` which ensures that blocks are produced even if there are no transactions. This is necessary for blocks to reach finality in a timely fashion.

### Wishlist

- Apply step backoff after skipping primaries, not before. Exponential backoff may allow for weakly synchronous network without failure.
- Faster finality by broadcasting signed `GOOD(Hash)` messages where Hash is a block hash of a block with number multiple of some epoch length. `GOOD` messages can be included in the seal.
