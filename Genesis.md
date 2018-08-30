---
title: Genesis
---

_Genesis_ is the very first block of a chain.

Every block in a chain has its parent, but Genesis is special and does not have one. Since there is no way to have a [consensus](Consensus) on the first block of the chain, it is usually hardcoded right into the client software. All other blocks depend on the Genesis and the whole network operates assuming that it is correct.

Usually, Genesis acts as a bootstrap for the chain and defines initial values of various system variables, like predefined balances of system accounts, public keys of authorities everyone agrees upon, etc.

In the case of [Substrate](Parity-Substrate), Genesis also contains an initial WASM image of the [Runtime](Runtime).

TODO: Substrate specifics.
