---
title: DApp Tutorial
---

**This is the third part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 2nd Part](Tutorial-Part-2.md) | [4th Part ⟶](Tutorial-Part-4.md)

----

## Parity Bonds

So now we're fairly happy with the idea of `Bond`s in our reactive user interface, it's time to get down and dirty with the blockchain. For this we will introduce the `oo7-parity` package, which provides a high-level reactive `Bond` API (it uses our low-level library *parity.js* under the hood, but you need not care).

To set this up, all we need do is import `bonds` from the `oo7-parity` module, so ensure you have this at the top of your file:

```js
import {bonds} from 'oo7-parity';
```

### 1. Watch the block

For our first trick, we will introduce the simplest of all bonds: `bonds.height`. This evaluates to [the number of the latest block](JSONRPC-eth-module#eth_blocknumber), expressed as a simple number.

In `app.jsx`, remove the entire `App` class and the two `const`s and replace it with:

```jsx
export class App extends React.Component {
	render() {
		return (
			<Rspan>{bonds.height}</Rspan>
		);
	}
}
```

You should already be familiar with the basic structure. If you try this, you'll see a single number tracking the latest height of your blockchain:

![image](https://cloud.githubusercontent.com/assets/138296/22700240/ee7d922c-ed27-11e6-8976-25acd1ffdecb.png)

It's not especially pretty, but you get the idea. We can bling it up easily enough by changing our rendering expression to:

{% raw %}
```jsx
<div>
	Current block is:
	&nbsp;
	<Rspan style={{fontWeight: 'bold'}}>
		{bonds.height.map(formatBlockNumber)}
	</Rspan>
</div>
```
{% endraw %}

We'll also need to provide the `formatBlockNumber` function:

```js
const formatBlockNumber = (n) => '#' + ('' + n).replace(/(\d)(?=(\d{3})+$)/g, "$1,");
```

Happily this is provided by the [`oo7-parity`](https://github.com/paritytech/oo7-parity) package, so you can alternatively just import it:

```js
import {bonds, formatBlockNumber} from 'oo7-parity';
```

And hey presto:

![image](https://cloud.githubusercontent.com/assets/138296/22700625/44b820f2-ed29-11e6-8fae-125303b677ce.png)

### 2. Blocks

The block number is great and all, but perhaps we're more interested in the latest block itself. Happily, Parity can help us there with the `bonds.blocks` object. This is a lazy-evaluated "array" of bonds which can subscribe to. To give yourself an idea of its capabilities, let's try it out in the console. First we'll expose the `bonds` object to the environment by adding this at the end of our object's constructor:

```js
window.bonds = bonds;
```

Having reloaded, quickly open the Chrome JS console, alter the environment to 127.0.0.1 and evaluate the block at number 69 with `bonds.blocks[69].log()`:

![image](https://cloud.githubusercontent.com/assets/138296/22701287/41c6e4f8-ed2b-11e6-94d9-d6b5e58cb911.png)

Notice that since it's all asynchronous, we must use the `.log()` trick to feed the result into the console (it's exactly equivalent to `.map(console.log)`). The result is, of course, the block object representing the 69th mined block on this chain.

Naturally, `bonds.blocks` is able to accept any number, even a bond, as its subscript. Let's make our dapp always give us the timestamp of the latest block.

{% raw %}
```jsx
<div>
	Latest block's timestamp is:&nbsp;
	<Rspan style={{fontWeight: 'bold'}}>
		{bonds.blocks[bonds.height].map(b => b.timestamp).map(_ => _.toString())}
	</Rspan>
</div>
```
{% endraw %}

![image](https://cloud.githubusercontent.com/assets/138296/22701622/26a80d68-ed2c-11e6-8720-946e311a9b34.png)

That `.map` is a bit cumbersome. Conveniently, the `Bond` API knows about subscripting, which means the `.timestamp` can be moved out of the `map`. (`toString` is a reserved expression, so it must remain `map`ped, however in general you will find that you rarely want to use such a blunt transformation anyway.)

Furthermore, `bonds.blocks[bonds.height]` is a fairly common expression. So much so that it has a shorter alias: `bonds.head`, so in fact the simplest means of expressing this becomes:

```js
{bonds.head.timestamp.map(_=>_.toString())}
```

### 3. Composing expressions

These expressions are sometimes very useful, but often you want to do some blockchain based computation on this information. For example, getting the author ("miner") of the most recent block is easy (`block.author`) but might be of limited use; in fact you might wish to know not just their identity but also their account balance.

Parity puts various means at your disposal to help you here:

- `bonds.balance(address)` evaluates to the current balance of account at `address`.
- `bonds.transactionCount(address)` evaluates to the current transaction count ("nonce") of account at `address`.
- `bonds.code(address)` evaluates to the current "contract" code of account at `address`.
- `bonds.storageAt(address, location)` evaluates to the value in storage `location` of account at `address`.

We'll use the first in our dapp to display the account balance of the most recent block author ("miner") - the expression we'll need is `bonds.balance(bonds.head.author)`. Left alone it's a bit ugly, so we'll prettify it a bit with the function `formatBalance` which is exposed by `oo7-parity`:

{% raw %}
```jsx
<div>
	Current block author's balance is:&nbsp;
	<Rspan style={{fontWeight: 'bold'}}>
		{bonds.balance(bonds.head.author).map(formatBalance)}
	</Rspan>
</div>
```
{% endraw %}

Ensure you have `formatBalance` available by replacing the line `import {formatBlockNumber} from 'oo7-parity';` with:

```js
import {bonds, formatBlockNumber, formatBalance} from 'oo7-parity';
```

![image](https://cloud.githubusercontent.com/assets/138296/22704760/d7468bcc-ed36-11e6-8411-320791d107e8.png)

You'll see it updating as the blocks go by.

### 4. Our balance

Parity can help us get information about our own accounts in a reactive manner. Several APIs are provided:

- `bonds.coinbase` evaluates to the node's current block authoring address.
- `bonds.accounts` evaluates to the list of accounts available for the dapp to use.
- `bonds.me` evaluates to the preferred account for this dapp to use. In Parity, this is the account visible in the Parity Signer at the bottom right of the page.
- `bonds.accountsInfo` evaluates to the mapping between dapp-visible addresses and account metadata.

To see the list of accounts available, we can just grab the list of accounts and join them with `', '`use:

```jsx
<div>
	Accounts available:&nbsp;
	<Rspan>
		{bonds.accounts.map(_=>_.join(', '))}
	</Rspan>
</div>
```

Determining the address of the preferred account is just as easy: it's just `bonds.me`. Switch the `render` function to use it:

```jsx
<div>
	Default account:&nbsp;
	<Rspan>{bonds.me}</Rspan>
</div>
```

Try this and use the Parity Signer's account selector to change accounts: you see the address change!

![image](https://cloud.githubusercontent.com/assets/138296/22710016/cad4cda0-ed49-11e6-9257-4dbd1885dc13.png)

...then selecting a different account gives...

![image](https://cloud.githubusercontent.com/assets/138296/22710003/c4168634-ed49-11e6-94af-177683da719d.png)

Maybe we'd like to know how much we have in our selected account, too. This can be done easily enough:

```jsx
<div>
	Default account:&nbsp;
	<Rspan>{bonds.me}</Rspan>
	<br />With a balance of&nbsp;
	<Rspan>
		{bonds.balance(bonds.me).map(formatBalance)}
	</Rspan>
</div>
```

![image](https://cloud.githubusercontent.com/assets/138296/22710059/f1572b8a-ed49-11e6-874a-7de1ff2ca519.png)

Maybe we would even like to understand what name we have given to the account (if any). We can ascertain this through the `bonds.accountsInfo` bond, which gives us a mapping between accounts and metadata. The expression which evaluates to the current account's name is therefore as simple as `bonds.accountsInfo[bonds.me].name`.

Our rendering function would now be:

```jsx
<div>
	Default account:&nbsp;
	<Rspan>{bonds.me}</Rspan>&nbsp;
	<br/>Given the name of&nbsp;<Rspan>
		{bonds.accountsInfo[bonds.me].name}
	</Rspan>
	<br/>With a balance of&nbsp;
	<Rspan>
		{bonds.balance(bonds.me).map(formatBalance)}
	</Rspan>
</div>
```

and should give something like:

![image](https://cloud.githubusercontent.com/assets/138296/22709662/8e12672a-ed48-11e6-96a3-be0065ff8cbe.png)

If you go ahead and either change the default account or even just edit its name else where in Parity Wallet, you'll see the page automatically track the changes, as you'd expect.

You should now be familiar with the core bonds in Parity and be happy to combine them into more complex expressions. Next up, we'll take a look at how we can use `Bond`s to interact with contracts that sit in the blockchain.

----

[4th Part ⟶](Tutorial-Part-4.md)
