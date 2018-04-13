---
title: DApp Tutorial
---

**This is the fourth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 3rd Part](Tutorial-Part-3.md) | [5th Part ⟶](Tutorial-Part-5.md)

----

## Calling Contracts

You should now be familiar with `Bond`s and some of the core Parity bond API. Next we'll put this knowledge to use with more complex expressions that will feature contracts.

Contract API basically comes in three pieces. Firstly, there's state-changing transactions like transferring tokens to a counter-party. Secondly, there's event reception and reporting that (usually) happen when such a state change occurs. Finally, there is inspection of the contract state through calling `constant` functions. For now we will restrict ourselves to the latter.

### 1. Our first contract

The first contract we will deal with is the global (name) registry. If you are not yet familiar, this is a registry that exists on all sensible blockchains which records fields of information for any desired name. The registry records ownership information so that names can be registered and their information changed at a later date.

The registry contract has a fairly simple API for inspecting. We only need to worry about two functions:

- `getOwner(bytes32) -> address` Given the Keccak hash of a name, this returns the address of its owner, if it has been reserved.
- `getData(bytes32, bytes32) -> bytes32` Given the Keccak hash of a name and a second field key, this returns the associated data.

There are subordinate functions to the latter such as `getAddress` and `getUint` which coerce the data into some other type. It is important to note that amongst the standardised field keys are:

- `A` the `address` primarily associated with this name; if you're wanting to send funds to the name, this is where to send them (assuming it's not null).
- `CONTENT` the `bytes32` which equals the Keccak hash of any content associated with this name. e.g. If the name represents a dapp, then this would be the hash of the dapp's content.
- `IMG` the `bytes32` which equals the Keccak hash of an associated image; this might be a persons's avatar or the dapp icon, depending on what is being named.

Let's begin by displaying the address associated with the name `'gavofyork'`. To do this we will need to create a special `Bond`-API contract object. The function to do this is `bonds.makeContract`; it takes the address and the ABI of the contract and returns an object with `Bond`-returning functions for each of the contract's functions.

The address is easy enough to find; this can be determined via the `parity.api.parity.registry` call. The ABI spec is rather long and can be derived from the contract code available at the ethcore/contracts repository. Since there is only a single canonical registry, Parity, conveniently constructs this for you and provides it at the `bonds.registry` object.

To figure out the primary associated address of the `gavofyork` name, we can use the `getAddress` call, together with the `parity.api.util.sha3` call to take the Keccak hash of our name. The full expression would be:

```js
bonds.registry.getAddress(parity.api.util.sha3('gavofyork'))
```

Typing `parity.api.util.sha3(...)` every time you want to look up a name in the registry gets tedious fast. Happily, Parity provides a number of derivative helper functions as part of the `bonds.registry` object: `lookupData`, `lookupAddress`, `lookupUint` and `lookupOwner`; they're all just like the `get`-prefixed brethren, but do the hashing for you. Our expression therefore can become:

```js
bonds.registry.lookupAddress('gavofyork', 'A')
```

Let's get this in to our dapp. Change the `render()`ed HTML to:

```
<div>
	gavofyork's address is&nbsp;
	<Rspan>{bonds.registry.lookupAddress('gavofyork', 'A')}</Rspan>
</div>
```

Refresh your dapp page and, assuming you're running on Kovan, you'll see something like:

![image](https://cloud.githubusercontent.com/assets/138296/22712813/2e36a65c-ed54-11e6-896d-c123bd95d3d5.png)

### 2. Dynamic lookups

Now this is all very well, but perhaps you're not just interested in my account, but want to allow the user to enter whatever account they desire. Bonds make it easy:

```jsx
export class App extends React.Component {
	constructor() {
		super();
		this.bond = new Bond;
	}
	render() {
		return (
			<div>
				Address of <InputBond bond={this.bond} placeholder='Lookup a name' /> is:<br/>
				<Rspan>{bonds.registry.lookupAddress(this.bond, 'A')}</Rspan>
				, it's balance is <Rspan>
					{bonds.balance(bonds.registry.lookupAddress(this.bond, 'A')).map(formatBalance)}
				</Rspan>
			</div>
		);
	}
}
```

Here we have rewritten the component to include a new `Bond` which, via the `InputBond`, we are using to represent the current text in the text input field. We are passing this into the `lookupAddress` function to turn it into a `Bond` equivalent to the address for that name in the registry, and using this as the `value` of a reactive `Hash` display component. We are also using it in conjunction with `bonds.getBalance` to display a formatted balance of the account.

Here's what it looks like:

![image](https://cloud.githubusercontent.com/assets/138296/22713122/2c8146e0-ed55-11e6-8809-c5329cf89bae.png)

If the name you are currently looking up happens to have its address changed meanwhile, or their balance changes, you will of course see these details reflected in real-time.

### 3. Derivative contracts

So far so good, but while the registry contract is interesting, it's not usually the final destination. Typically the registry is used to lookup the address of a second contract that you would actually like to use.

Let's suppose that second contract is the GithubHint contract; if you're not already familiar, the GithubHint contract allows you to suggest which URLs might serve content for a particular hash. It's a semi-centralised, hacky alternative to content-addressable-delivery systems like BitTorrent/Kademlia, Swarm and IPFS. We use it widely in Parity as a means of content dissemination.

Since it's a "standard" contract in Parity, the ABI for it is available in `oo7-parity` as `GitHubHintABI`. The address changes per chain, but can be discovered via the registry under the name `'githubhint'`; the expression would therefore be `bonds.registry.lookupAddress('githubhint', 'A')`.

An important thing to realise about the `makeContract` function is that it does not require a "plain" address for the contract, but can actually work with a `Bond` for the address; everything will magically react if the address to which the `Bond` evaluates changes.

Therefore our GithubHint contract object can be created with the expression:

```js
bonds.makeContract(
	bonds.registry.lookupAddress('githubhint', 'A'),
	GitHubHintABI);
```

Though we mustn't forget to import `GitHubHint`:

```js
import {GitHubHintABI} from 'oo7-parity';
```

The GithubHint contract has only a single inspection method: `entries`. This takes a `bytes32` (the hash of the content to be found) and returns three items (via an array). There are three kinds of entry; Github repository entries, whereby the first and second items form the address of a particular commit of a particular repository; general URLs, where the first item is a URL and the second is the null hash; and empty entries where both items are null. The third item is always the owner (if any) of the entry and the only account capable of changing the hint information.

In this small demo, we'll assume that we are looking up only URLs and so are only interested in the first item. We therefore want an expression like:

```js
GithubHint.entries(hash)[0]
```

where `hash` is some content hash and `GithubHint` is the contract object.

Putting this all together we can change our dapp to:

```jsx
export class App extends React.Component {
	constructor() {
		super();
		this.bond = new Bond;
		this.GithubHint = bonds.makeContract(bonds.registry.lookupAddress('githubhint', 'A'), GitHubHintABI);
	}
	render() {
		return (
			<div>
				URL for content <HashBond bond={this.bond} floatingLabelText='Content-hash' /> is:<br/>
				<Rspan>{this.GithubHint.entries(this.bond)[0]}</Rspan>
			</div>
		);
	}
}
```

Note that we are using `HashBond` from `oo7-react` rather than `InputBond`. This just ensures that we enter only valid 32-byte hashes. Ensure that the import line is changed to:

```jsx
import {InputBond, HashBond} from 'parity-reactive-ui';
```

When you refresh the page enter the hash of a some content you know has a URL hint, for example:

`0xd40679a3a234d8421c678d64f4df3308859e8ad07ac95ce4a228aceb96955287`

Then watch the URL come up!

![image](https://cloud.githubusercontent.com/assets/138296/22715524/3f1545cc-ed5f-11e6-9536-f7fd9fed423d.png)


### Further refinements

Let's display the image associated with a registered name - we want to type `gavofyork` and have my mug come up. First we'll need to import the reactive version of the `img` element (the `Rimg` component) from the `oo7-react` package, so we should change the `oo7-react` import line:

```
import {Rspan, Rimg} from 'oo7-react';
```

Next, let's alter the dapp's render `div`:

```
<div>
	<InputBond bond={this.bond} placeholder='Name' />
	<Rimg src={this.GithubHint.entries(bonds.registry.lookupData(this.bond, 'IMG'))[0]} />
</div>
```

The only interesting thing here is the fact we're looking up the `IMG` entry of the entered name, passing it into GithubHint and taking the URL item from the result.

Fire it up and type the name of an entry which has an `IMG` field (e.g. `gavofyork`):

![image](https://cloud.githubusercontent.com/assets/138296/22715677/1e3e2be2-ed60-11e6-9609-16475996e7f3.png)

And after enough time to download, you should see the avatar!

Now that you're familiar with how to inspect the state of contracts, in the next tutorial, we'll look into state-changing APIs, starting with making simple transactions.

----

[5th Part ⟶](Tutorial-Part-5.md)
