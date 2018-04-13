---
title: Automatic Updating
---

Parity Ethereum features the first fully blockchain-based consensus-protocol updating system and quite possibly the most secure software auto-update system ever deployed.

Deployed on the chain is a contract `Operations`. If you're interested you can go view it [here](https://github.com/paritytech/contracts/blob/master/Operations.sol) in our contracts repository. This acts as an oracle, tracking a number of important aspects about the blockchain you are running (i.e. foundation or kovan) and the Parity client (though it's written to work with each of the other implementations should their authors so desire). It includes information about:
* all hard and soft forks,
* which client releases support which fork,
* what release track (stable, beta) each of the releases are on,
* what the client binaries are for each platform,
* which client development teams would vote for which fork.
Basically, everything you might want to know about a multi-stakeholder consensus-protocol.

Using this information, Parity Ethereum can do three important things completely automatically:
* avoid continuing to sync a chain after a fork when it knows that its own logic doesn't support the new protocol;
* automatically download the most recent release in the background;
* seamlessly upgrade itself to a new release in situ, either automatically or via a button in Parity Wallet.
