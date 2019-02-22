---
title: Automatic Updating
---

Parity Ethereum features the first fully blockchain-based consensus-protocol updating system; which can be considered *"reasonably secure"* if the upstream binaries are trusted.

Deployed on the chain is the [`Operations` contract](https://github.com/parity-contracts/auto-updater). This acts as an oracle, tracking a number of important aspects about the blockchain you are running (i.e. foundation or kovan) and the Parity client (though it's written to work with each of the other implementations should their authors so desire). It includes information about:
* all hard and soft forks,
* which client releases support which fork,
* what release track (stable, beta) each of the releases are on,
* what the client binaries are for each platform,
* which client development teams would vote for which fork.
Basically, everything you might want to know about a multi-stakeholder consensus-protocol.

Using this information, Parity Ethereum can do three important things completely automatically:
* avoid continuing to sync a chain after a fork when it knows that its own logic doesn't support the new protocol;
* automatically download the most recent release in the background;
* seamlessly upgrade itself to a new release in situ.

## Client Configuration

The snippet below shows the config items relevant to the autoupdater, set to their defaults.

```toml
[parity]
# Which releases should be autoupdated to, possible values:
# "critical" - network-wide security or consensus relevant updates only, "all", "none"
auto_update = "critical"
# Which release-track should you get updates for, possible values:
# "current" - same as node is currently running
# "beta" - new features and bugfixes
# "stable" - bugfixes only
# "nightly" - built from github master and may not always be available
release_track = "current"
# After becoming aware of an update, the maximum number of blocks before it is applied
# (in practice there is a random wait for less than this number of blocks for network health)
auto_update_delay = 100
# Number of blocks between checks for updates
auto_update_check_frequency = 20
# Disables auto downloading of new releases. Not recommended.
no_download = false
# Forces Parity to run even if there are known issues regarding consensus. Not recommended.
no_consensus = false
```

## Chain Configuration

in order to use the autoupdater on your private chains, a [registrar contract](https://github.com/parity-contracts/name-registry) must be deployed, the key `operations` is used to denote the address of the contract that manages the auto-update process.

You are required to specify the address of the registrar contract in the general params section of your chain specification:
```json
"params": {
"registrar" : "0x<address of registrar contract here>",
},
```

and then [`reserve`](https://github.com/parity-contracts/name-registry/blob/ded6d0ed960b044ef5fcfc2486a5075d1965e4d3/contracts/SimpleRegistry.sol#L71) the `operations` key and [`setData`](https://github.com/parity-contracts/name-registry/blob/ded6d0ed960b044ef5fcfc2486a5075d1965e4d3/contracts/SimpleRegistry.sol#L108) to the address of the previously deployed `operations` contract. 

### Operations Contract

A simple example of an operations contract can be found [here](https://github.com/parity-contracts/auto-updater/blob/master/contracts/SimpleOperations.sol#L109).

There are three types of actors associated with the operations contract
 * the contract owner, who manages and maintains the contract itself, and "chain-wide" settings like fork blocks.
 * client owners, who manage and maintain the clients connected to the chain in question.
 * users, who read from the operations contract to download and verify releases based on their hashes.

the operations contract allows you to create [releases](https://github.com/parity-contracts/auto-updater/blob/5496aa730b242d4ffbd2846497718068d7062bdf/contracts/SimpleOperations.sol#L75), and [builds](https://github.com/parity-contracts/auto-updater/blob/5496aa730b242d4ffbd2846497718068d7062bdf/contracts/SimpleOperations.sol#L109) of those releases, for multiple clients on a  blockchain.
