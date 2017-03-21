**This is the third part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 2nd Part](https://github.com/paritytech/parity/wiki/Tutorial-Part-II) | [4th Part ⟶](https://github.com/paritytech/parity/wiki/Tutorial-Part-IV)

----

## Parity Bonds

So now we're fairly happy with the idea of `Bond`s in our reactive user interface, it's time to get down and dirty with the blockchain. This particular dapp asks our hosting environment (in this case Parity but potentially others in the future) to populate the Javascript global environment with the [parity.js API](https://www.npmjs.com/package/@parity/parity.js).

If you're interested, this happens in `dist/index.html` at the line:

```js
<script src="/parity-utils/parity.js"></script>
```

This gives us a global `parity` object which we can use to get information concerning the blockchain. In addition to the basic *parity.js* API, we also introduce the `oo7-parity` package, which provides a higher level reactive `Bond` API on top of *parity.js*.

This is already set up for us, but if you're interested, it happens in `src/client/scripts/entry.jsx`, specifically the lines:

```js
import {setupBonds, abiPolyfill} from 'oo7-parity';

// …

// Polyfills for parity.js
parity.api.abi = abiPolyfill();

// We use and dirty up the global namespace here.
parity.bonds = setupBonds(parity.api);
```

The `abiPolyfill` call just provides various standard contract ABIs for us in `parity.api.abi`; the bonds API is setup later with the `setupBonds` call.

### Watch the block

For our first trick, we will introduce the simplest of all bonds: `parity.bonds.blockNumber`. This evaluates to [the current block](https://github.com/paritytech/parity/wiki/JSONRPC-eth-module#eth_blocknumber), expressed as a simple number.

In `app.jsx`, remove the entire `App` class and replace it with:

```jsx
export class App extends React.Component {
	render() {
		return (
			<Rspan>{parity.bonds.blockNumber}</Rspan>
		);
	}
}
```

You should already be familiar with the basic structure. If you try this, you'll see a single number tracking the latest height of your blockchain:

![image](https://cloud.githubusercontent.com/assets/138296/22700240/ee7d922c-ed27-11e6-8976-25acd1ffdecb.png)

It's not especially pretty, but you get the idea. We can bling it up easily enough by changing our rendering expression to:

```jsx
<div>
	Current block is:
	&nbsp;
	<Rspan style={{fontWeight: 'bold'}}>
		{parity.bonds.blockNumber.map(formatBlockNumber)}
	</Rspan>
</div>
```

We'll also need to provide the `formatBlockNumber` function:

```js
const formatBlockNumber = (n) => '#' + ('' + n).replace(/(\d)(?=(\d{3})+$)/g, "$1,");
```

Happily this is provided by the [`oo7-parity`](https://github.com/paritytech/oo7-parity) package, so you can alternatively just import it:

```js
import {formatBlockNumber} from 'oo7-parity';
```

And hey presto:

![image](https://cloud.githubusercontent.com/assets/138296/22700625/44b820f2-ed29-11e6-8fae-125303b677ce.png)

### Blocks

The block number is great and all, but perhaps we're more interested in the latest block itself. Happily, Parity can help us there with the `parity.bonds.blocks` object. This is a lazy-evaluated "array" of bonds which can subscribe to. To give yourself an idea of its capabilities, let's quickly open the Chrome JS console alter the environment to 127.0.0.1 and evaluate the block at number 69:

![image](https://cloud.githubusercontent.com/assets/138296/22701287/41c6e4f8-ed2b-11e6-94d9-d6b5e58cb911.png)

Notice that since it's all asynchronous, we must use the `.then(console.log)` trick to feed the result into the console. The result is, of course, the block object representing the 69th mined block on this chain.

Naturally, `parity.bonds.blocks` is able to accept any number, even a bond, as its subscript. Let's make our dapp always give us the timestamp of the latest block.

```jsx
<div>
	Latest block's timestamp is:&nbsp;
	<Rspan style={{fontWeight: 'bold'}}>
		{parity.bonds.blocks[parity.bonds.blockNumber].map(b => b.timestamp)}
	</Rspan>
</div>
```

![image](https://cloud.githubusercontent.com/assets/138296/22701622/26a80d68-ed2c-11e6-8720-946e311a9b34.png)

That `.map` is a bit cumbersome. Conveniently, the `Bond` API knows about subscripting, and that expression is reducible to:

```js
{parity.bonds.blocks[parity.bonds.blockNumber].timestamp}
```

Indeed, `parity.bonds.blocks[parity.bonds.blockNumber]` is a fairly common expression. So much so that it has a shorter alias: `parity.bonds.block`, so in fact the simplest means of expressing our `<Rspan>` expression is:

```js
{parity.bonds.block.timestamp}
```

### Composing expressions

These expressions are sometimes very useful, but often you want to do some block-chain based computation on this information. For example, getting the author ("miner") of the most recent block is easy (`parity.block.author`) but might be of limited use; in fact you might wish to know not just their identity but also their account balance.

Parity puts various means at your disposal to help you here:

- `parity.bonds.balance(address)` evaluates to the current balance of account at `address`.
- `parity.bonds.transactionCount(address)` evaluates to the current transaction count ("nonce") of account at `address`.
- `parity.bonds.code(address)` evaluates to the current "contract" code of account at `address`.
- `parity.bonds.storageAt(address, location)` evaluates to the value in storage `location` of account at `address`.

We'll use the first in our dapp to display the account balance of the most recent block author ("miner") - the expression we'll need is `parity.bonds.balance(parity.bonds.block.author)`. Left alone it's a bit ugly, so we'll prettify it a bit with the function `formatBalance` which is exposed by `007-parity`:

```jsx
<div>
	Current block author's balance is:&nbsp;
	<Rspan style={{fontWeight: 'bold'}}>
		{parity.bonds.balance(parity.bonds.block.author).map(formatBalance)}
	</Rspan>
	&nbsp;wei
</div>
```

![image](https://cloud.githubusercontent.com/assets/138296/22704760/d7468bcc-ed36-11e6-8411-320791d107e8.png)

You'll see it updating as the blocks go by.

### Our balance

Parity can help us get information about our own accounts in a reactive manner. Several APIs are provided:

- `parity.bonds.coinbase` evaluates to the node's current block authoring address.
- `parity.bonds.accounts` evaluates to the list of accounts available for the dapp to use.
- `parity.bonds.defaultAccount` evaluates to the preferred account for this dapp to use. In Parity, this is the account visible in the Parity Signer at the bottom right of the page.
- `parity.bonds.accountsInfo` evaluates to the mapping between dapp-visible addresses and account metadata.

To see the list of accounts available, we can just grab the list of accounts and join them with `', '`use:

```jsx
<div>
	Accounts available:&nbsp;
	<Rspan>
		{parity.bonds.accounts.map(_=>_.join(', '))}
	</Rspan>
</div>
```

Determining the address of the preferred account is just as easy. Rather than using the reactive `span` element, let's use an alternative reactive element, `Hash`. This is very similar to the `Rspan` element, except we give it a `value` prop rather than children and it renders our value nicely as an abridged hash:

```jsx
<div>
	Default account:&nbsp;
	<Hash value={parity.bonds.defaultAccount} />
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
	<Hash value={parity.bonds.defaultAccount} />
	<br />With a balance of&nbsp;
	<Rspan>
		{parity.bonds.balance(parity.bonds.defaultAccount).map(formatBalance)}
	</Rspan>
</div>
```

![image](https://cloud.githubusercontent.com/assets/138296/22710059/f1572b8a-ed49-11e6-874a-7de1ff2ca519.png)

Maybe we would even like to understand what name we have given to the account (if any). We can ascertain this through the `parity.bonds.accountsInfo` bond, which gives us a mapping between accounts and metadata. The expression which evaluates to the current account's name is therefore as simple as  `parity.bonds.accountsInfo[parity.bonds.defaultAccount].name`.

Our rendering function would now be:

```jsx
<div>
	Default account:&nbsp;
	<Rspan>
		{parity.bonds.accountsInfo[parity.bonds.defaultAccount].name}
	</Rspan>
	&nbsp;<Hash value={parity.bonds.defaultAccount} />
	<br/>With a balance of&nbsp;
	<Rspan>
		{parity.bonds.balance(parity.bonds.defaultAccount).map(formatBalance)}
	</Rspan>
</div>
```

and should give something like:

![image](https://cloud.githubusercontent.com/assets/138296/22709662/8e12672a-ed48-11e6-96a3-be0065ff8cbe.png)

If you go ahead and either change the default account or even just edit its name else where in Parity Wallet, you'll see the page automatically track the changes, as you'd expect.

You should now be familiar with the core bonds in Parity and be happy to combine them into more complex expressions. Next up, we'll take a look at how we can use `Bond`s to interact with contracts that sit n the blockchain.

----

[4th Part ⟶](https://github.com/paritytech/parity/wiki/Tutorial-Part-IV)
