---
title: Byzantine Fault Tolerance
---

_Byzantine Fault Tolerance_ is a property that allows several parties to reach consensus (usually for the next block in the chain) without explicitly or implicitly trusting each other.

### Introduction

As an owner of a bank account, the last thing you want is a situation where you and your bank disagree on the amount of money you have. Blockchain allows parties to reach consensus without trusting each other (hence zero-trust), even in the presence of malicious participants who are actively trying to break the system and steal your money.

This is accomplished with a property called [Byzantine Fault Tolerance](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance). BFT algorithms typically require garnering majority or supermajority support for some proposed state change in the form of signed votes or statements. Malicious nodes are considered as those that attempt to vote several times or proposing different things to different nodes (i.e. sending a vote for block A to one peer, and a vote for block B to another).

The nodes that were proven to have misbehaved would be penalized and their vote ignored. Their revealing is possible because of the [pigeonhole principle](https://en.wikipedia.org/wiki/Pigeonhole_principle), which says that as long as the number of messages sent is greater than the number of nodes (it will be as long as nodes have multiple peers and messages are forwarded), then one node would have sent two conflicting messages.

The node that detected misbehavior must escalate it by broadcasting an _evidence of misbehavior_, a special message that consists of received conflicting proposals and/or commitments signed by the same key. That way, the network would quickly know about the misbehaving node and exclude it from the poll.

It was mathematically proven that this protocol is viable as long as _strictly more than two thirds of the nodes_ in the network are not malicious and operate according to the protocol. This is one of the reasons why it is important to have a lot of nodes in the network.

### Implementation

Substrate provides an existing implementation of BFT consensus that can be used almost out of the box. Depending on the usage scenario, you may either use existing block authoring logic or provide your own. In the latter case, you may use the generic implementation of the BFT and adapt it to your needs.

It should be noted though, that Substrate, being very much a work in progress, will eventually move to more advanced consensus algorithms like Hybrid Consensus.

Also, since Substrate is a fully extendable and customizable framework, you may define your own consensus algorithm potentially having nothing in common with the BFT. In fact, Substrate is so flexible that it may support solutions that are not based on a classic blockchain architecture.

