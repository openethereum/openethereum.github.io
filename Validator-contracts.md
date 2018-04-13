---
title: Validator Contracts
---

## Majority List

This contract starts with an initial set of validators supporting each other. Validator can add or remove support given to as many addresses as they want. If an address is supported by more than a half of validators then it becomes a validator, similarly if support for a validator falls below a half then they are removed.
Support can not be added once `MAX_VALIDATORS` is reached. Validators are automatically reported if they misbehave and support is removed after `MAX_INACTIVITY` period.

The following is the user facing ABI that can be used to interact with the contract:

```json
[{"constant":true,"inputs":[{"name":"validator","type":"address"}],"name":"highSupport","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"","type":"uint256"}],"name":"validatorsList","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"}],"name":"removeValidator","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"}],"name":"addValidator","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"validator","type":"address"}],"name":"getRepeatedBenign","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"validator","type":"address"}],"name":"getSupported","outputs":[{"name":"","type":"address[]"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"MAX_VALIDATORS","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValidators","outputs":[{"name":"","type":"address[]"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"reporter","type":"address"},{"name":"validator","type":"address"}],"name":"firstBenignReported","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"validator","type":"address"}],"name":"getSupport","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"}],"name":"addSupport","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"}],"name":"absolveFirstBenign","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"MAX_INACTIVITY","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"validator","type":"address"},{"name":"blockNumber","type":"uint256"}],"name":"reportBenign","outputs":[],"payable":false,"type":"function"},{"inputs":[],"payable":false,"type":"constructor"},{"payable":true,"type":"fallback"},{"anonymous":false,"inputs":[{"indexed":true,"name":"parent_hash","type":"bytes32"},{"indexed":true,"name":"nonce","type":"uint256"},{"indexed":false,"name":"new_set","type":"address[]"}],"name":"ValidatorsChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"reporter","type":"address"},{"indexed":true,"name":"reported","type":"address"},{"indexed":true,"name":"malicious","type":"bool"}],"name":"Report","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"supporter","type":"address"},{"indexed":true,"name":"supported","type":"address"},{"indexed":true,"name":"added","type":"bool"}],"name":"Support","type":"event"}]
```

### Inspecting the state
- `getValidators` method lists the current validators
- `getSupport` returns the number of validators what support a given validator
- `getSupported` lists all addresses supported by the given validator

### Adding support
To support an address to become a validator execute the `addSupport` with that address.

### Removing support
Removing support should be done by executing `reportBenign` twice, second time being after `MAX_INACTIVITY` seconds.

### Absolving a validator
After executing `reportBenign` a validator may change their mind. They should then execute `absolveFirstBenign` to forget about it.

### Inactivity
If a validator misses a block nodes will automatically execute `reportBenign`, thus if inactivity exceeds or repeats after `MAX_INACTIVITY` period, support will be removed.

### Malicious behaviour
If a validator runs two signing nodes or modifies the code to attempt to create forks other nodes will automatically remove support for them.
