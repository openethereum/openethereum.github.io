---
title: Byzantine Fault Tolerance
---

_Byzantine Fault Tolerance_ is one of the consensus algorithms and is a way for several parties to vote for something (usually for the next block in the chain) without explicitly or implicitly trusting each other.

### Introduction

As an owner of a bank account, the last thing you want is to have a situation wherein you and your bank disagree on the amount of money you have. Blockchain allows parties to reach consensus without trusting each other (hence zero-trust), even in presence of malicious participants who are actively trying to break the system and steal your money.

This is done using an algorithm called [Byzantine Fault Tolerance](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance). The basic idea is that nodes vote for the next state in two steps. First, each node broadcasts its _proposal_ on what it thinks the state should be. Second, by listening to proposals from other nodes, the first node may change its mind and _commit_ to another state. Or it may stick to its original proposal and commit it again. Nodes should always vote for the most present version in the network.

However, nodes must propose and commit only once per round. Since both the proposal and commitment are signed by the node key, all other nodes may track what proposals and commitments were for all other nodes. This prevents malicious nodes from skewing the results in favor of a particular outcome by voting several times, or even ruining the voting by proposing different things to different nodes.

The nodes that were proven to misbehave would be penalized and their vote will be ignored. This is possible because of the [pigeonhole principle](https://en.wikipedia.org/wiki/Pigeonhole_principle), which basically says there is a node that would observe a misbehavior of another node by receiving two or more conflicting proposals or commitments.

The node that detected misbehavior must escalate it by broadcasting an _evidence of misbehavior_, a special message which consists of received conflicting proposals and/or commitments signed by the same key. That way, the network would quickly know about the misbehaving node and it would be excluded from the poll.

It was mathematically proven that this protocol is viable as long as _strictly more than two thirds of the nodes_ in the network are not malicious and operate according to the protocol. This is one of the reasons why it is important to have a lot of nodes in the network.

### Implementation

Substrate provides an existing implementation of BFT consensus that can be used almost out of the box. Depending on a usage scenario, you may either use existing block authoring logic, or provide your own. In the latter case, you may use the generic implementation of the BFT and adapt it to your needs.

It should be noted though, that Substrate, being very much a work in progress, eventually would move to a more advanced consensus algorithms like the Hybrid Consensus.

Also, since Substrate is a fully extendable and customizable framework, there is a possibility to define your own consensus algorithm potentially having nothing in common with the BFT. In fact, Substrate is so flexible that it may support solutions that are not based on a classic blockchain architecture.

