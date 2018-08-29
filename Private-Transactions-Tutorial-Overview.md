---
title: Private Transactions Tutorial Overview
---

In this tutorial, we will learn step by step how to setup a Private contract and make private transactions. The use case is the following:

Alice and Bob would like to use a smart-contract on a blockchain, but want to make sure no-one else can read the state of the contract or interact with it.

To follow this tutorial, you need to have a set of Secret Store nodes running. Follow the [Secret Store tutorial](https://wiki.parity.io/Secret-Store-Tutorial-overview) to get familiar with it. This tutorial will pick-up from there.
 

To keep things simple we will work on a [Private development chain](Private-development-chain) but the setup presented would work on any blockchain, Private or Public.


The overall setup looks as follow:

- 2 regular user accounts (Alice and Bob) 
- 3 Secret Store nodes to manage the contract and transaction's encryption key
- 1 Permissioning contract allowing Bob and Alice to interact with the Secret Store
- 1 Private contract that only Alice and Bob can interact with.


## Table of contents:

* [Part 1 - Configuring each node](Private-Transactions-Tutorial-1)
  * [0. Prerequisite](Private-Transactions-Tutorial-1.md#0-prerequisite)
  * [1. Configure Alice's node](Private-Transactions-Tutorial-1.md#1-configure-alices-node)
  * [2. Configure Bob's node](Private-Transactions-Tutorial-1.md#2-configure-bobs-node)
  * [3. Finalize configurations](Private-Transactions-Tutorial-1.md#3-finalize-configurations)
  * [3.1 Collect enodes's addresses](Private-Transactions-Tutorial-1.md#31-collect-enodess-addresses)
    * [3.2 Manage Alice and Bob's keys](Private-Transactions-Tutorial-1.md#32-manage-alice-and-bobs-keys)
    * [3.3 Update Alice and Bob's configuration files](Private-Transactions-Tutorial-1.md#33-update-alice-and-bobs-configuration-files)

* [Part 2 - Permissioning contract](Private-Transactions-Tutorial-2)
  * [1. Deploying a permissioning contract](Private-Transactions-Tutorial-2.md#1-deploying-a-permissioning-contract)
  * [2. Update the Secret Store configuration files](Private-Transactions-Tutorial-2.md#2-update-the-secret-store-configuration-files)

* [Part 3 - Private contract deployment](Private-Transactions-Tutorial-3)
  * [1. Get the private contract's bytecode](Private-Transactions-Tutorial-3.md#1-get-the-private-contracts-bytecode)
  * [2. Compose and sign the contract's transaction](Private-Transactions-Tutorial-3.md#2-compose-and-sign-the-contracts-transaction)
  * [3. Encrypt and broadcast the deployment transaction](Private-Transactions-Tutorial-3.md#3-encrypt-and-broadcast-the-deployment-transaction)
    * [3.1 Encrypt the deployment transaction for the validators](Private-Transactions-Tutorial-3.md#31-encrypt-the-deployment-transaction-for-the-validators)
    * [3.2 Sign and broadcast the deployment transaction](Private-Transactions-Tutorial-3.md#32-sign-and-broadcast-the-deployment-transaction)
  * [4. Overview](Private-Transactions-Tutorial-3.md#4-overview)

* [Part 4 - Send private transactions](Private-Transactions-Tutorial-4.md)
  * [1. See the value of x](Private-Transactions-Tutorial-4.md#1-see-the-value-of-x)
  * [2. Change the value of x](Private-Transactions-Tutorial-4.md#2-change-the-value-of-x)
    * [2.1 Compose the transaction](Private-Transactions-Tutorial-4.md#21-compose-the-transaction)
    * [2.2 Sign and send the transaction](Private-Transactions-Tutorial-4.md#22-sign-and-send-the-transaction)
  * [3. Verify the state change](Private-Transactions-Tutorial-4.md#3-verify-the-state-change)


|[ Part 1 - Configuring each node → ](Private-Transactions-Tutorial-1.md)|


