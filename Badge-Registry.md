---
title: Badge Registry
---

Similar to the [Token Registry](Token-Registry.md), the Parity Ethereum wallet allows certifying badges to [Accounts and Wallets](Accounts%2C-Wallets%2C-Vaults). Prominent examples of badges used by Parity are the SMS- and Email-verification badges on Kovan testnet enabling users with certified accounts to use the built-in faucet.

This article covers creating a new certifier, registering a new badge with the badge registry and setting meta-data like title and icon.

### Certifier Contract

The Certifier contract interface for badges is comparable to the ERC-20 interface for [tokens](Tokens.md). Basically any contract implementing or inheriting from [`Certifier.sol`](https://github.com/parity-contracts/name-registry/blob/master/contracts/Certifier.sol) can be used to create new badges.

To get started quickly, it's worth to have a look at the OPRAH badge contract [`OprahBadge.sol`](https://github.com/paritytech/contracts/blob/master/OprahBadge.sol) which implements the Certifier. You can deploy your certifier to the network and start to `certify()` accounts.

### Registering a new Badge

To display badges in the Parity Wallet, your certifier needs to be registered with the Parity Badge Registry. Depending on which network you are working with, the parity wallet logs the used registry to the JavaScript console, just open it and get the contract address, for Foundation it's:

    [lookupAddress] badgereg: 0xF74A6e92588eE0a524b44b251E0b47d1A15824f7

This address can be used on the Contracts view to watch the Badge Registry contract by specifying the contract address (see above) and the contract ABI:

    [{"constant":false,"inputs":[{"name":"_new","type":"address"}],"name":"setOwner","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_addr","type":"address"},{"name":"_name","type":"bytes32"}],"name":"register","outputs":[{"name":"","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"fromName","outputs":[{"name":"id","type":"uint256"},{"name":"addr","type":"address"},{"name":"owner","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"badgeCount","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_fee","type":"uint256"}],"name":"setFee","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_id","type":"uint256"},{"name":"_key","type":"bytes32"}],"name":"meta","outputs":[{"name":"","type":"bytes32"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"drain","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_id","type":"uint256"}],"name":"unregister","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_id","type":"uint256"},{"name":"_newAddr","type":"address"}],"name":"setAddress","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_addr","type":"address"}],"name":"fromAddress","outputs":[{"name":"id","type":"uint256"},{"name":"name","type":"bytes32"},{"name":"owner","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_id","type":"uint256"}],"name":"badge","outputs":[{"name":"addr","type":"address"},{"name":"name","type":"bytes32"},{"name":"owner","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_id","type":"uint256"},{"name":"_key","type":"bytes32"},{"name":"_value","type":"bytes32"}],"name":"setMeta","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_addr","type":"address"},{"name":"_name","type":"bytes32"},{"name":"_owner","type":"address"}],"name":"registerAs","outputs":[{"name":"","type":"bool"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[],"name":"fee","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"},{"indexed":true,"name":"id","type":"uint256"},{"indexed":false,"name":"addr","type":"address"}],"name":"Registered","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"},{"indexed":true,"name":"id","type":"uint256"}],"name":"Unregistered","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"id","type":"uint256"},{"indexed":true,"name":"key","type":"bytes32"},{"indexed":false,"name":"value","type":"bytes32"}],"name":"MetaChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"id","type":"uint256"},{"indexed":false,"name":"addr","type":"address"}],"name":"AddressChanged","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"old","type":"address"},{"indexed":true,"name":"current","type":"address"}],"name":"NewOwner","type":"event"}]

This enables you to query (call) the contract directly from the UI to list existing badges and retrieve metadata. To register your own badge, click Execute, select the `register()` method, provide the contract address of your certifier and chose a name.

### Adding Meta-Data

To get the badge properly displayed in the Parity Wallet, it's handy to register an Icon and a Title in the Badge registry for the meta-data of your registered certifier.

To set a title, execute the Badge Registry Contract method `setMeta`, and put in the id of your badge entry in the registry, the TITLE key `0x5449544c45` and the actual title value.

To set an icon, register the URL of the icon first with the [Parity Github Hint](Parity-Github-Hint.md) DApp, and register the returning hash as value for the IMG key `0x494d47`.

Your Icon and Title should be visible on all certified accounts now.
