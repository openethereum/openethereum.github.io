---
title: Validator Set Tutorial Overview
---

This tutorial will walk you through the setup of a private blockchain using [Aura](Aura) consensus mechanism and a dynamic validator set contract.

No previous knowledge of OpenEthereum or Aura is required.

Aura (for "Authority Round") is a proof-of-authority (PoA) consensus mechanism that lets a defined set of authorities seal blocks in a round robin fashion. The term validator is also used in place of an authority node. Unlike proof-of-work (PoW) based consensus mechanism, PoA-based consensus makes sense for private consortium blockchains or networks using a currency without value such as test networks where there is no incentive for miners to spend money in mining. Kovan testnet is running on Aura.  
There are different ways to define the set of authorities in the [chain specification file](Pluggable-Consensus#aura). The easiest way is to use a fixed list of authorities. Any change in this list requires a hard fork, and thus an off-chain synchronisation between the parties running authority nodes. Kovan network historically ran with a fixed list of authorities.

A fixed list of addresses works well for small networks with few participants. However, it lacks flexibility as there is no easy way to add or remove validators, there is also no monitoring mechanism to report if authority nodes are offline. Using a contract, however, allows managing authorities without a hard fork as well as reporting misbehaving authorities. This tutorial will show the setup of a network using a fixed list of validators at its start and then move to a Validator-set smart contract to manage authorities. Finally, we will use a simple script to monitor authorities and spot the misbehaving ones.

The overall setup is as follows:

- Two authority accounts (`Node0` and `Node1`) 
- One standard account receiving RPC requests (`Alice`)

If you wish to read this tutorial and quickly get to the end result, you can clone [this repository](https://github.com/Tbaut/Validator-set-tutorial/tree/master/Full-setup) and spin up the nodes with an already set up blockchain.

## Table of contents:

* [Part 1 - Configuring each node](Validator-Set-Tutorial-1#title-part-1---configuring-each-node)
  * [1. Create the genesis file](Validator-Set-Tutorial-1#1-create-the-genesis-file)
  * [2. Create and configure the validators' nodes](Validator-Set-Tutorial-1#2-create-and-configure-the-validators-nodes)
       * [2.1 Create and configure Node0](Validator-Set-Tutorial-1#21-create-and-configure-node0)
       * [2.2 Set Node0 as validator in the genesis](Validator-Set-Tutorial-1#22-set-node0-as-validator-in-the-genesis)
       * [2.3 Create and configure Node1](Validator-Set-Tutorial-1#23-create-and-configure-node1)
   * [3. Create and configure Alice's node](Validator-Set-Tutorial-1#3-create-and-configure-alices-node)
   * [4. Add bootnodes to each configuration file](Validator-Set-Tutorial-1#4-add-bootnodes-to-each-configuration-file)

* [Part 2 - Deploy the Validator Set contract](Validator-Set-Tutorial-2#title-part-2---deploy-the-validator-set-contract)
  * [1. Set up Remix and remixd](Validator-Set-Tutorial-2#1-setup-remix-and-remixd)
  * [2. Deploy RelaySet and RelayedOwnedSet](Validator-Set-Tutorial-2#2-deploy-relayset-and-relayedownedset)

* [Part 3 - Hardfork to use the Validator Set contract](Validator-Set-Tutorial-3#title-hardfork-to-use-the-validator-set-contract)
  * [1. Chose the hardfork block](Validator-Set-Tutorial-3#1-chose-the-hardfork-block)
  * [2. Add Node1 as validator](Validator-Set-Tutorial-3#2-add-node1-as-validator)
  * [3. Network Overview](Validator-Set-Tutorial-3#3-network-overview)
  
* [Part 4 - Visualize misbehaviors](Validator-Set-Tutorial-4#title-visualize-misbehaviors)
  * [1. Let Node0 be down](Validator-Set-Tutorial-4#1-let-node0-be-down)
  * [2. Track missbehaviors in a Dapp](Validator-Set-Tutorial-4#2-track-missbehaviors-in-a-dapp)


|[ Part 1 - Configuring each node → ](Validator-Set-Tutorial-1)|
