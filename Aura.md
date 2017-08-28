Aura is one of the Blockchain consensus algorithms available in Parity. It is capable of tolerating up to 50% of malicious nodes with chain reorganizations possible up to a limited depth dependent on the number of validators, after which finality is guaranteed.

The consensus can be ran with `--force-sealing` which ensures that blocks are produced even if there are no transactions. This is necessary for fault tolerance described.

This consensus requires a [`validators`](https://github.com/paritytech/parity/wiki/Consensus-Engines#validator-engines) to be specified, which determines the list of `n_v` blockchain addresses at each height `h` which participate in the consensus.

A node can represent a validator when it is ran with `--engine-signer VALIDATOR-ADDRESS`.

Sealing is the act of collecting transactions and attaching a header to produce a block.

## Primary
Primary is a node representing a validator which is entitled to seal and broadcast a block at a given time. Block should be always sealed on top of the latest known block in the canonical chain (based on chain scoring defined later).

Each node in Aura keeps track of a step `s` which is based on the UNIX time `t`. The current step is equal to `t / step_duration`. At each step the primary is chosen to be the `s % n_v`th validator.

## Chain scoring
The chain score is calculated using the height and step of the highest block: `U128_max * h - s`. Block with the highest chain score is the canonical one.

## Header
Header includes the step and the primary signature of the block hash (not including the step) in the header seal field. For light client support an additional field with validator list hash may be added.

## Verification
Verification checks if the signature belongs to the correct primary for the given step. Blocks from more than 1 step into the future are rejected.

## Finality
If more than 50% of the nodes signed on a chain and then they signed again on those signatures, the chain is final. This means finality can be achieved within `2 * step_duration * n_v`. A lower bound might exist.
