---
title: Parity Name Registry
---

The Parity name registry is an easy way to lookup and address with a human-readable key. Internally to Parity it is the only contract address that is hardcoded for a specific network, i.e. Parity is configured out of the box to provide the address for the Registry when configured to run on either the Ethereum Homestead or Morden networks.

**WARNING** There is a non-refundable fee associated with registering a name. Dropping a name will not return this fee. Only interact with the dapp and contract if you are certain you know what you are doing and understand the risks.

## Background

Internally to the [Registry contract](https://github.com/paritytech/contracts/blob/master/Registry.sol) names are stored as sha3 hashes of the name registered, this name mapping directly to an address. Initially this approach is very beneficial to dapp and contract developers, i.e. by doing a name lookup on the Registry, any address for the network can be returned without having to hard-code it per network.

An example of this would be a dapp **bob's amazin stuff** that has contracts on both Homestead and Morden. Normally a dapp developer would need to hardcode the contract addresses per network and each time a new version is deployed, have to update the contract and the address in the dapp for each network. By using the Registry, a name can be reserved `bobsamazinstuff` that will resolve to the correct addresses irrespective of network.

## Name registration

Name registration can be done with with the basic dapp that is provided as a pre-configured dapp as part of the Parity 1.4+ interface. Simply specify the name, associate with an address and it will be available.

## Name lookup

Parity has an namespaced API that will return the registry address for a specific network. Using the `parity.js` interface, a name lookup for `gavcoin` would be -


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


## Future work

Parity is looking at supporting transfers to names that can be found in the Registry. As an example instead of saying "send me 1 ETH to `0x012345...abcde`" you will be able to say "send me 1 ETH to `joeyswallet`"
