# The `ethcore` Module

These are RPCs specific to the operation of Parity.

### Block authoring (aka "mining")
- `ethcore_extraData` `()` *returns* `d: DATA` Get the current extra data used `d`.
- `ethcore_defaultExtraData` `()` *returns* `d: DATA` Get the default extra data `d`.
- `ethcore_gasFloorTarget` `()` *returns* `t: QUANTITY` Get the gas-floor target `t`.
- `ethcore_gasCeilTarget` `()` *returns* `t: QUANTITY` Get the gas-ceiling target `t`.
- `ethcore_minGasPrice` `()` *returns* `p: QUANTITY` Get the minimal allowable gas price in Wei `p`.
- `ethcore_transactionsLimit` `()` *returns* `` ???

### Development
- `ethcore_devLogs` `()` *returns* `[s0: STR, s1: STR, ...]` Get the array of recent logs `s0`, `s1`, ....
- `ethcore_devLogsLevels` `()` *returns* `` ???

### Network Information
- `ethcore_netChain` `()` *returns* `s: STR` Get the chain on which the node is operating `s`.
- `ethcore_netMaxPeers` `()` *returns* `i: INT` Get the maximum amount of peers `i`.
- `ethcore_netPort` `()` *returns* `i: INT` Get the port on which we are listening `i`.
- `ethcore_nodeName` `()` *returns* `s: STR` Get the node's identifying name `s`.
- `ethcore_rpcSettings` `()` *returns* `` ???
- `ethcore_gasPriceStatistics` `()` *returns* `[i0: QUANTITY, i1: QUANTITY, ... i8: QUANTITY]` get the octile distribution of recent transactions' gas prices; `i0` is the minimum, `i4` the median and `i8` the maximum.
- `ethcore_unsignedTransactionsCount` `()` *returns* `` ???
- `ethcore_generateSecretPhrase` `()` *returns* `s: STR` get a cryptographically secure secret phrase with around 160 bits of entropy.
