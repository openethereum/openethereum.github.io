---
title: Register your DApp for discovery
---

The following page explains how to register your DAPP to allow Parity discovering it, loading it from the network and displaying it in the Applications tab of the Parity UI.

The entire process requires using 2 Smart Contracts. At the time of this article, one of them as a UI, the other must be registered manually.

We will be using the following Dapps/Contracts:

 - GitHubHint (GHH)
 - DappReg

## Check your assets

Before we get started, let´s check that you have everything we need at hand:

 - an ID for our DAPP
 - 1 ETH to register it (this amount will be lost and not recoverable)
 - a `manifest.json` file
 - a logo image
 - a bundle located on GitHub


## Preparation

### Generate a UID

We will first generate a unique ID for our DAPP. Calculate the a sha3 checksum as:

    web3.sha3('<DappId> v<DappVersion> from <yourName>')

Replace `DappId`, `DappVersion` and `YourName` by the appropriate values.
This will produce our `UID`.

### Register/Watch the DappReg contract

In Parity UI, go to the `Contracts` tab and click `Watch`. Select the `custom` option.

Provide the contract's address:

 - Homestead: `0xD70994d7020DF8052A1124561ff548f3b88744d8`
 - Ropsten: `0x724A8602fc0C2b346f8eC56Df2913710742d3fD0`
 - Kovan: `0xD36d48C274af1a169857C9721e122aa023A0eE01`
 - Morden: `0x11e869F9094a1101B4C60201d6Cf894AfC7EadBB`

The ABI can be [found on etherscan](https://etherscan.io/address/0xD70994d7020DF8052A1124561ff548f3b88744d8#code).

It is:

    [{"constant":true,"inputs":[{"name":"_id","type":"bytes32"},{"name":"_key","type":"bytes32"}],"name":"meta","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"count","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"_new","type":"address"}],"name":"setOwner","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"_id","type":"bytes32"}],"name":"unregister","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"_fee","type":"uint256"}],"name":"setFee","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"_id","type":"bytes32"}],"name":"get","outputs":[{"name":"id","type":"bytes32"},{"name":"owner","type":"address"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"_id","type":"bytes32"},{"name":"_key","type":"bytes32"},{"name":"_value","type":"bytes32"}],"name":"setMeta","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[],"name":"drain","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"_id","type":"bytes32"},{"name":"_owner","type":"address"}],"name":"setDappOwner","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"fee","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"_index","type":"uint256"}],"name":"at","outputs":[{"name":"id","type":"bytes32"},{"name":"owner","type":"address"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"_id","type":"bytes32"}],"name":"register","outputs":[],"payable":true,"type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"id","type":"bytes32"},{"indexed":true,"name":"key","type":"bytes32"},{"indexed":false,"name":"value","type":"bytes32"}],"name":"MetaChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"id","type":"bytes32"},{"indexed":true,"name":"owner","type":"address"}],"name":"OwnerChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"id","type":"bytes32"},{"indexed":true,"name":"owner","type":"address"}],"name":"Registered","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"id","type":"bytes32"}],"name":"Unregistered","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"old","type":"address"},{"indexed":true,"name":"current","type":"address"}],"name":"NewOwner","type":"event"}]

Confirm to add this contract to your list.


## GitHubHint

You will find the DAPP for GitHubHint in the Applications.

First ensure that the right account is selected. For each of the following, provide the path and register it to get a hash:

- image (your DAPP logo)
- manifest.json
- content

After confirming those transactions, the hashes will disappear. To recall them, paste your links again. This time the hashes will appears in Orange, confirming their registration.

Take note of the 3 hashes.

## DappReg

At the moment, there is no DAPP/UI yet available for this contract so we will interact with it manually from the `Contracts` tab of the Parity UI.

First `Execute` the the register function and make sure to:

- select the right 'owner' account. This is the only account allowed to make changes later
- provide the `UID` we got from the sha3 call at the beginning
- send 1 ETH while calling this function

Confirm your transaction and wait until it is processed.

Once your `UID` has been registered, you can use the `setMeta` function on the following keys:

- IMG
- MANIFEST
- CONTENT

**NOTE**: Everything is uppercase.

For each, you will pass the corresponding hash we got in the previous step.

Once all the transactions are processed (this can take a few minutes), your DAPP will be registered and will appear in the Applications tab in the Parity UI.

