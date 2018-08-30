---
title: Parity Substrate
---

Next-generation framework for blockchain innovation.

At its heart, Substrate is a combination of three technologies: WebAssembly, Libp2p and AfG Consensus. It is both a library for building new blockchains and a "skeleton key" of a blockchain client, able to synchronize to any Substrate-based chain.

Substrate chains have three distinct features that make them "next-generation": a dynamic, self-defining state-transition function (STF), light-client functionality from day one and a progressive consensus algorithm with fast block production and adaptive, definite finality.

The STF, encoded in WebAssembly, is known as the "runtime". This defines the execute_block function and can specify everything from the staking algorithm, transaction semantics, logging mechanisms and procedures for replacing any aspect of itself or of the blockchain’s state ("governance"). Because the runtime is entirely dynamic all of these can be switched out or upgraded at any time. A Substrate chain is very much a "living organism".

More information on the [GitHub repository](https://github.com/paritytech/substrate)
