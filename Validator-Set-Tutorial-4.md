---
title: Visualize misbehaviours
---

Now that we have two validators responsible for creating blocks on the network, we need to make sure they perform their task.
Misbehaviour reporting is implemented in the contracts we've deployed, and it will allow any other validator to report misbehaviour as well as the users of the network such as Alice to see which node misbehaved.

## 1. Let `Node0` be down

Let's shut down `Node0` for a moment and see what happens for `Alice`. In this case I've shut it down at block #1001:

```bash
2018-09-30 19:21:00  Imported #1000 0xfae7…79a5 (0 txs, 0.00 Mgas, 0 ms, 0.57 KiB)
2018-09-30 19:21:05  Imported #1001 0xb5fd…e938 (0 txs, 0.00 Mgas, 0 ms, 0.57 KiB)
2018-09-30 19:21:07     1/25 peers   174 KiB chain 192 KiB db 0 bytes queue 21 KiB sync  RPC:  0 conn,    4 req/s,   64 µs
2018-09-30 19:21:15  Imported #1002 0xe083…0799 (0 txs, 0.00 Mgas, 0 ms, 0.57 KiB)
2018-09-30 19:21:25  Imported #1003 0xd185…58c4 (1 txs, 0.03 Mgas, 0 ms, 0.74 KiB)
2018-09-30 19:21:35  Imported #1004 0xd043…f833 (0 txs, 0.00 Mgas, 0 ms, 0.57 KiB)
```

Having a look at the block time, it went from every 5sec to every 10sec. This is expected as `Node0` misses his turn and does not issue blocks anymore.

In the same time, having a look at `Node1`'s console output shows the following:

```bash
2018-09-30 19:21:05  Imported #1001 0xb5fd…e938 (0 txs, 0.00 Mgas, 1 ms, 0.57 KiB)
2018-09-30 19:21:15  Reported benign validator misbehaviour 0xa292…d64a
2018-09-30 19:21:15  Imported #1002 0xe083…0799 (0 txs, 0.00 Mgas, 0 ms, 0.57 KiB)
2018-09-30 19:21:25  Reported benign validator misbehaviour 0xa292…d64a
2018-09-30 19:21:25  Transaction mined (hash 0x696a3876a68e129d82aa7c4ce67fae824a571ca9b0c6da522c7b854910595986)
2018-09-30 19:21:25  Imported #1003 0xd185…58c4 (1 txs, 0.03 Mgas, 0 ms, 0.74 KiB)
2018-09-30 19:21:30     1/25 peers   100 KiB chain 192 KiB db 0 bytes queue 17 KiB sync  RPC:  0 conn,    0 req/s,    0 µs
```

`Node1` expected to receive a block from `Node0`, and as this did not happen, `Node1` will report misbehaviours by sending a transaction to call `ReportBenign` function on `RelaySet` contract with the proof.

## 2. Track misbehaviours in a Dapp

The contract is emitting events for each misbehaviour. In Aura, there are currently two types of misbehaviours recognised and reported as stated in [the specifications](Validator-Set.html#reporting-contract). It means that any user such as Alice can track the nodes misbehaving.
- a benign misbehaviour is when a validator does not issue a block.
- a malicious misbehaviour is when a validator tries to create a fork and issues two different blocks for a given step.

In this tutorial, we will track both but will only test the benign ones. To monitor the events from the contract, use the following javascript code saved in a file called `watchEvent.js`. It is based on [`web3.js`](https://web3js.readthedocs.io/en/1.0/getting-started.html). You will need to install `web3.js` library with [npm](https://www.npmjs.com/get-npm).

```js
"use strict";

const Web3 = require("web3");

const LOCAL_WS = "ws://localhost:8546";
let provider = new Web3.providers.WebsocketProvider(LOCAL_WS);
const web3 = new Web3(provider);

const log = (...args) => console.log(new Date(), ...args);
const error = (...args) => console.error(new Date(), ...args);

provider.on("error", error => error("WS error", error));
provider.on("end", error => {
  error("WS connection closed.");
});

const relayedOwnedSetAddress = "0x3D6f28e83e31bc475dEf77A0F01ECEDe4FBe2d22";
const from = "0x12db1ee91481573302f63ebf3d735820081c68a2";


(async () => {
  var relayedOwnedSetABI=[{"constant": true,"inputs": [],"name": "recentBlocks","outputs": [{"name": "","type": "uint256"}],"payable": false,"stateMutability": "view","type": "function"},{"constant": true,"inputs": [],"name": "getPending","outputs": [{"name": "","type": "address[]"}],"payable": false,"stateMutability": "view","type": "function"},{"constant": false,"inputs": [{"name": "_new","type": "address"}],"name": "setOwner","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": false,"inputs": [{"name": "_validator","type": "address"}],"name": "removeValidator","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": false,"inputs": [{"name": "_validator","type": "address"}],"name": "addValidator","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": false,"inputs": [{"name": "_reporter","type": "address"},{"name": "_validator","type": "address"},{"name": "_blockNumber","type": "uint256"},{"name": "_proof","type": "bytes"}],"name": "relayReportMalicious","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": false,"inputs": [],"name": "finalizeChange","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": false,"inputs": [{"name": "_recentBlocks","type": "uint256"}],"name": "setRecentBlocks","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "owner","outputs": [{"name": "","type": "address"}],"payable": false,"stateMutability": "view","type": "function"},{"constant": true,"inputs": [],"name": "relaySet","outputs": [{"name": "","type": "address"}],"payable": false,"stateMutability": "view","type": "function"},{"constant": true,"inputs": [],"name": "finalized","outputs": [{"name": "","type": "bool"}],"payable": false,"stateMutability": "view","type": "function"},{"constant": true,"inputs": [],"name": "getValidators","outputs": [{"name": "","type": "address[]"}],"payable": false,"stateMutability": "view","type": "function"},{"constant": false,"inputs": [{"name": "_relaySet","type": "address"}],"name": "setRelay","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": false,"inputs": [{"name": "_reporter","type": "address"},{"name": "_validator","type": "address"},{"name": "_blockNumber","type": "uint256"}],"name": "relayReportBenign","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"inputs": [{"name": "_relaySet","type": "address"},{"name": "_initial","type": "address[]"}],"payable": false,"stateMutability": "nonpayable","type": "constructor"},{"anonymous": false,"inputs": [{"indexed": false,"name": "currentSet","type": "address[]"}],"name": "ChangeFinalized","type": "event"},{"anonymous": false,"inputs": [{"indexed": true,"name": "reporter","type": "address"},{"indexed": true,"name": "reported","type": "address"},{"indexed": true,"name": "malicious","type": "bool"}],"name": "Report","type": "event"},{"anonymous": false,"inputs": [{"indexed": true,"name": "old","type": "address"},{"indexed": true,"name": "current","type": "address"}],"name": "NewOwner","type": "event"}]
  let relayedOwnedSet = new web3.eth.Contract(relayedOwnedSetABI, relayedOwnedSetAddress);
  relayedOwnedSet.events.Report()
    .on('data', event => {
      var isMalicious = (typeof(event.malicious)=="undefined" || event.malicious )? "no" : "yes" ;
      log("Reporter: " + event.returnValues.reporter + " Reported: " + event.returnValues.reported + " Malicious: "+ isMalicious);
    })
    .on('error', error => error("Report listener error", error));
})();
```

This code initiates a WebSockets connection to Alice's node and listens to the events emitted by the `RelayedOwnedSet` contract.
As `Node0` is currently offline, we can see that `Node1` `0x3278..3179E` is sending reports about benign (non malicious) misbehaviors regarding `Node0` `0xA292..d64A`. These are sent every 10sec:

```bash
$ node watchEvents.js
2018-09-30T17:57:55.025Z 'Reporter: 0x32784591C38Bf8aA081e96Ba4DB462BD73a3179E Reported: 0xA2922fEc00bb29FE13D68E87F64b9Ad1719Ed64A Malicious:  no'
2018-09-30T17:58:15.093Z 'Reporter: 0x32784591C38Bf8aA081e96Ba4DB462BD73a3179E Reported: 0xA2922fEc00bb29FE13D68E87F64b9Ad1719Ed64A Malicious:  no'
```


|[ ← Part 3 - Hardfork to use the Validator Set contract ](Validator-Set-Tutorial-3)|
