---
title: P2P (Peer To Peer)
---

P2P (Peer To Peer) is a common name for a set of technologies used to create decentralized networking applications.

The main idea is to have a self-sustaining network environment where every participant (usually called a node) is capable of operating within a network without any prior configuration or interaction with an authority.

Typically, nodes discover each other by sending broadcast messages and exchanging routing information as well as lists of their known peers (a neighboring node is called a peer). Also, nodes usually have a list of the bootstrap nodes with known network addresses that are used to populate peer lists for the first time.

Once bootstrapped, such a network may operate without any maintenance. Individual nodes may join or leave the network at any time, but it does not affect the overall network connectivity. 

However, in the case of a major networking failure, the whole swarm may be temporarily split into several disjoint parts. Whilst such scenario is nearly impossible in a modern world with global connectivity, yet, for blockchain applications, it needs to be always kept in mind, since it may pose a serious threat to chain stability. [Consensus](Consensus) algorithms of a chain must be aware of such possibility and must be designed in a way that would resolve the situation properly.

[Substrate](Parity-Substrate) uses [libp2p](https://libp2p.io/) for these purposes.
