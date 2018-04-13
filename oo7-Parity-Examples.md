---
title: oo7 Examples
---

Examples of common operations and useful functionality when building a dApp using [oo7-Bonds](oo7-Parity-Reference.md). It does not aim to cover the complete functionality, for more features please look at the documentation.

## Bond functionality (oo7)
We need to install and require the [oo7](https://www.npmjs.com/package/oo7) Bond package first.
`$ npm i oo7`
```
const oo7 = require('oo7');
const Bond = oo7.Bond;
```

In simple a bond is just a reactive promise. This means it will always be up to date with the newest value change.
You can tie a function to it, which executes each time a value change triggers the bond.
```
var bond = new Bond();  // creates a Bond instance
bond.tie(console.log);  // ties the function to the bond => gets executed on each value change
bond.then(console.log); // executes once on the next value change => bond.log() is equivalent.
var transBond = bond.map(v => `${new Date} ${v}` ); // creates a new TransformBond.
```
Transformbonds are dependent on their dependencies (here the initial bond). If the value changes it will trigger to execute the provided function and return the result as value. The difference between using `tie` and `then` is that latter will only trigger once and behaves as a Promise call would. While tying a function will execute it each time the value updates until you untie it.
```
var id = transBond.tie(console.log); // Tie console to the TransformBond to watch when it triggers on value change.
bond.changed(32);  // You change the value of the bond.
window.setTimeout(() => {bond.changed('new value')}, 1000); // After one second change the value again
transBond.untie(id); // You can also untie a function again
```
Now we can see the power of bonds. While the first change will still behave like a normal promise and triggers our various console outputs, the second change just a second later will again reactivate the promise. So we can be sure to always operate on the most recent value.

For example we can easily create joint Bonds which trigger when all values are ready. Or TransformBonds with multiple dependencies. They are in fact all extending the `Bond` class and this behaving the same but have additional features (e.g. transform-function, dependent on other bonds) on top.

```
var bond2 = new Bond();
var jointBond = Bond.all([bond, bond2]); // create a joint Bond, which triggers when the given bonds have a ready value
var transJointBond = jointBond.map(([v1,v2]) => `Joint Bond val1: ${v1} , val2: ${v2}`); // create a TransformBond
transJointBond.log(); // print out result once when bond triggers
```

Since our initial bond value already has a 'ready value' (not undefined), it will trigger the jointBond (which triggers its TransformBond) with the two most recent values if we run `bond2.changed(64);`.

We can also create TransformBonds with multiple dependencies. Its constructor gets as arguments a function which is executed when triggered, the bonds whose values are needed for the result(ready values will trigger) and dependency bonds which are needed for the computation indirectly but whose values we do not need (only knowledge that they are updated).
```
const {TransformBond, TimeBond} = oo7;
var timeBond = new TimeBond(); // inbuilt bond which is returning the UNIX timestamp
var tBond = new TransformBond(([v1,v2]) => `Transformed Bond Args ${v1} and ${v2}`, [bond, bond2], [timeBond]);
```

All this gets especially useful when we want to interact with the blockchain and easily use inbuilt functions to keep us always with the newest state of the chain. We explore some of them in the following.

## Blockchain interaction ([oo7-parity](oo7-Parity-Reference.md))
The setup is slightly more complicated now, but the simplest is to clone the parity-skeleton app and configure it as explained [here](Tutorial-Part-1.md).

### Some common functions
```
bonds.head; // equivalent to 'bonds.blocks[bonds.height];' Most recent block with all information details
bonds.time; // TimeBond - UNIX timestamp
bonds.accountsInfo; // Information about your accounts (Address, Name..)
bonds.me; // selected accounts address
bonds.author; // client coinbase address
```

### Some compound functions
* Balance of selected account
```
bonds.balance(bonds.me); // given an address (or Bond which returns) it gives the ether balance of the account.
```
* Transactions of the most recent block
```
// We set resolveDepth to 1 (second argument to map function), so the bonds.transaction TransformBond gets resolved to the actual values.
var txnsBond = bonds.head.map(b => b.transactions.map(txHash => bonds.transaction(txHash)), 1);
txnsBond.then(txns => txns.forEach(console.log));
```
* Post a transaction
```
bonds.post({to:bonds.me, value:1 * 1e15}).tie(console.log(o));
```
* Information for an address (e.g. contract)
```
var info = x => Bond.all([bonds.code(x), bonds.storageAt(x), bonds.balance(x), bonds.nonce(x)]);
info(bonds.me).then(arr => arr.forEach(x => x.log())); // arrays or TransformBonds do not get resolved in depth, thus manually
info(bonds.me); // triggers the details to the address to be printed
```

There is much more functionality and we will extend the list over time. However to see a more complete version take a look at the [oo7-parity reference](oo7-Parity-Reference.md). This library is also still very much in development and thus might change recently.

## Bond & React (oo7-react)
To really use the advantages the Bond-libraries give us we need to be able to have updating display components which support Bonds as input. Therefore we have [oo7-react](https://github.com/paritytech/oo7-react). It provides necessary standard components such as `div`, `span` or `a` and some more blockchain specific ones like `Hash`.

In general the standard components are just preceeded by a 'R' for 'Reactive'. E.g. `Rdiv`, `Rspan`, `Ra`. They take Bonds as input naturally and will update their state when the Bond triggers.
```
this.bond = new Bond.changed('0xd40679a3a234d8421c678d64f4df3308859e8ad07ac95ce4a228aceb96955287');
...

render () {
  return (
    <Rdiv>
      <Rspan>{bonds.time}</Rspan> {/*displays the time reactively*/}
      <Ra href={bonds.githubhint.entries(this.bond)[0]}> A githubhint link </Ra> {/*displays the link which can be updated with githubhint*/}
      <Rimg src={this.bond} /> {/*displays the given bonds image*/}
      <Hash value={bonds.head.map(b => b.hash)} /> {/*displays the hash value in a short format*/}
    </Rdiv>
  );
}
```

## Blockchain specific components ([parity-reactive-ui](https://github.com/paritytech/parity-reactive-ui/tree/master))
The 4th and last library is [parity-reactive-ui](https://github.com/paritytech/parity-reactive-ui) which provides small reusable components which will make UI development for the blockchain very efficient and fast.
It includes currently components to display `Account` , `Transaction` and `Block` details and components to manage `Input`.

Displaying Account information.
{% raw %}
```
this.addressBond = new Bond();
this.balance = new Bond();
...

render () {
  return (
    <Rdiv>
      <AddressBond bond={this.addressBond} /> {/*Validates address input*/}
      <BalanceBond bond={this.balance} /> {/*Balance input*/}
      <AccountLabel address={this.addressBond} /> {/*Displays account of address*/}
      <TransactButton content='Transact to the account' tx={{to: this.address, value: this.balance}} />  {/*Button shows Transaction state*/}
    </Rdiv>
  );
}
```
{% endraw %}

And with some simple lines you got a transaction interace.

![Screen_of_Rendered](http://i.imgur.com/bOqFFXn.png)

 A full list of components, their properties and usage in an example dApp can be found in the Github repo. If there are any questions left feel free to join us on [gitter(js)](https://gitter.im/paritytech/parity.js) or [gitter(main)](https://gitter.im/paritytech/parity) and ask.
