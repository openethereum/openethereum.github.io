---
title: Block Reward Contract
---

Parity's consensus engine allows using a smart contract for block reward calculation. The engine
passes a list of benefactors and reward types to the contract which then returns a list of addresses
and respective rewards.

## Contract

The block reward contract has the following interface:

```solidity
interface BlockReward {
    // produce rewards for the given benefactors,
    // with corresponding reward codes.
    // only callable by `SYSTEM_ADDRESS`
    function reward(address[] benefactors, uint16[] kind)
        external
        returns (address[], uint256[]);
}
```

The kind array should have the same length as the benefactors' array. The function should return two
arrays of equal length. One containing addresses to reward, and the other the corresponding balance
increase.

Currently the engine supports the following types of rewards:

- 0 - Author - Reward attributed to the block author
- 2 - Empty step - Reward attributed to the author(s) of empty step(s) included in the block (AuthorityRound engine)
- 3 - External - Reward attributed by an external protocol (e.g. block reward contract)
- 101-106 - Uncle - Reward attributed to uncles, with distance 1 to 6 (Ethash engine)

The reward contract will receive as input reward types of 0, 2 or 101-106, and any reward attributed by it
will be labelled as external (this is visible when tracing rewards).

The contract [ABI](https://github.com/parity-contracts/block-reward/blob/6c645a93a0ae1eb0a34bfe85071e4ce36deed3b3/abis/BlockReward.json) as well as an example contract can be found [here](https://github.com/parity-contracts/block-reward).

## AuthorityRound

AuRa is currently the only engine supporting a block reward contract. It can be enabled by defining
the address of the block reward contract in the [chainspec](Chain-specification) and a block to
activate it:

```json
"engine": {
  "authorityRound": {
    "params": {
      ...
      "blockRewardContractAddress": "0x0000000000000000000000000000000000000042",
      "blockRewardContractTransition": "0x42"
    }
  }
}
```

If no `blockRewardContractTransition` is provided, it is assumed that the contract exists from genesis. You can define a constructor to deploy the contract when starting the chain:

```json
"accounts": {
  ...
  "0000000000000000000000000000000000000042": {
    "balance": "1",
    "constructor": "6060604052341561000f57600080fd5b6102b88061001e6000396000f300606060405260043610610041576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff168063f91c289814610046575b600080fd5b341561005157600080fd5b610086600480803590602001908201803590602001919091929080359060200190820180359060200191909192905050610125565b604051808060200180602001838103835285818151815260200191508051906020019060200280838360005b838110156100cd5780820151818401526020810190506100b2565b50505050905001838103825284818151815260200191508051906020019060200280838360005b8381101561010f5780820151818401526020810190506100f4565b5050505090500194505050505060405180910390f35b61012d610264565b610135610278565b61013d610278565b600073fffffffffffffffffffffffffffffffffffffffe73ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff1614151561018d57600080fd5b85859050888890501415156101a157600080fd5b878790506040518059106101b25750595b90808252806020026020018201604052509150600090505b815181101561021d5785858281811015156101e157fe5b9050602002013561ffff166103e80161ffff16828281518110151561020257fe5b906020019060200201818152505080806001019150506101ca565b878783828280806020026020016040519081016040528093929190818152602001838360200280828437820191505050505050915090915093509350505094509492505050565b602060405190810160405280600081525090565b6020604051908101604052806000815250905600a165627a7a723058201da0f164e75517fb8baf51f030b904032cb748334938e7386f63025bfb23f3de0029"
  }
}
```

## Limitations

Currently events emitted by the block reward contract when the engine calls `reward` are not
observable. This is a limitation of how "system" transactions are implemented at the moment.
