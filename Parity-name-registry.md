---
title: Parity Name Registry
---

The Parity name registry is an easy way to look up an address using a human-readable key. It is the only contract address that is hardcoded in Parity UI per network.

**WARNING** There is a non-refundable fee associated with registering a name. Dropping a name will not return this fee. Only interact with the dapp and contract if you are certain you know what you are doing and understand the risks.

## Background

Internally, the [Registry contract](https://github.com/parity-contracts/name-registry/blob/master/contracts/Registry.sol) names are stored as sha3 hashes of the name registered, this name mapping directly to an address. This approach is very beneficial to dapp and contract developers, because by doing a name lookup on the Registry, the correct address for the name in this network can be returned without having to hard-code the address for each network.

An example of this would be a dapp **bob's amazin stuff** that has contracts on both Homestead and Morden. Before, a dapp developer needed to hardcode the contract addresses for each network and update them each time a new version is deployed. By using the Registry, a name can be reserved `bobsamazinstuff` that will resolve to the correct addresses irrespective of the network.

## Name registration

Name registration can be done easily using the dapp _'Registry'_ in Parity UI. Simply specify the name and associate it with an address and it will be available.

## Name lookup

Parity has a namespaced API that will return the registry address for a specific network. Using the `parity.js` interface, a name lookup for `gavcoin` would be:

```js
  api.parity
    .registryAddress()
    .then((registryAddress) => {
      console.log(`the registry was found at ${registryAddress}`);

      const registry = api.newContract(registryAbi, registryAddress).instance;

      return registry.getAddress.call({}, [api.util.sha3('gavcoin'), 'A']);
    })
    .then((contactAddress) => {
      console.log(`gavcoin was found at ${contactAddress}`);
    });
```

## Future work

Parity is looking into supporting transfers to names that can be found in the Registry. As an example, instead of saying "send me 1 ETH to `0x012345...abcde`" you will be able to say "send me 1 ETH to `joeyswallet`".
