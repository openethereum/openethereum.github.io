---
title: DApp Tutorial
---

**This is the ninth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 8th Part](Tutorial-Part-8.md) | [10th Part ⟶](Tutorial-Part-10.md)

----

## Deploying a Contract

For our penultimate trick, we will introduce the ability to not just use, but also deploy the contract from our fledgling dapp. Deploying a contract is very similar to any other kind of transaction; we use a slightly different API (`bonds.deployContract`) in order to conveniently return us with a `Contract` object rather than just a transaction receipt as with the standard `bonds.post` API.

There is backwards compatibility, so we can use the same `TransactionProgressBadge` in order to display the progress of the deployment transaction as it gets signed and confirmed.

### 1. Split and Prop

Before we can begin in earnest, we will need to refactor some of our existing code. Whereas at the moment, our dapp heads straight into the "voting" mode we actually want to create a new mode of operation where we are deploying the underlying contract. To do this, we will rename the class `App` to `Counter` (and remove the `export` functionality) and introduce a new exported class `App`.

The new class `App` will, for now, just `render()` a `Counter` object. However, the one key change we'll make here is to pass in the `Counter` contract as a prop named `contract`. This has a two repercussions since we're working in React. Firstly, we should move the definitions of `this.voted`, `this.prevVote` and `this.prevVotes` from the constructor into `componentWillReceiveProps` and `componentWillMount`, so that they respond to changes in the prop value.

Secondly, rather than storing the `Counter` contract object in `App`'s '`this.counter` we'll place it in the more correct `this.state.counter`, so that should it change (which it will, but not quite yet), it is reflected in the browser through forcing a new `render()`.

Here's the final code for class `App`:

```js
export class App extends React.Component {
	constructor () {
		super();
		this.state = { counter: bonds.makeContract('0x7aC77Cb854E064f22E747F40b90FE6D6Bc1e3197', CounterABI) };
	}
	render () {
		return (<div>
			<Counter contract={this.state.counter} />
		</div>);
	}
}
```

The `render` function of the new `Counter` class is mostly equivalent to the same function in the original `App` class. The only change is to replace the two lines that use `this.counter` to instead use `this.props.contract`, which is now being passed from `App`. The two props lines for the `VoteOption` element `votes` and `vote` now read:

```js
votes={this.props.contract.votes(i)}
vote={() => this.setState({tx: this.props.contract.vote(i)})}
```


The `constructor` portion of the new `Counter` class is a little different from the `App` class. It now reads:

```js
constructor() {
	super();
	this.state = { tx: null };
}
componentWillMount () { this.componentWillReceiveProps(this.props); }
componentWillReceiveProps (props) {
	this.voted = this.props.contract.hasVoted(bonds.me);
	this.prevVote = this.props.contract.Voted({ who: bonds.me });
	this.prevVotes = this.props.contract.Voted({ who: bonds.accounts });
}
```

See how the bonds' setup have been moved out of the constructor (inert to prop changes) into the React APIs functions which execute along with prop changes.

With these changes, the dapp will remain essentially the same, however the ground is now prepared for introducing the deployment mode.

### 2. The Real Deal

`Counter` class is ready for our needs; our efforts can now be concentrated on the `App` class, into which we'll add an initial mode for deploying the contract.

One of the first things we need to remove is the hard-coded contract address. Furthermore, when we do deploy the contract, we'll need to track the transaction which is doing it. So we will swap out the state initialiser line in our constructor for one that initialises both the `counter` and a deployment `tx` to `null`:

```js
this.state = { tx: null, counter: null };
```

We'll also add a `deploy` function, whose job it is to sort out the deployment, once the user initiates it. Since we'll need to call it from an event handler, we'll pre-bind it to `this` in the constructor:

```js
this.deploy = this.deploy.bind(this);
```

For the new `deploy()` function, we'll use the `deployContract` API to create a new transaction. It requires the contract's bytecode (which we'll place in a `const CounterCode` next) and the ABI (which we already have in `CounterABI`). The function begins thus:

```
deploy () {
	let tx = bonds.deployContract(CounterCode, CounterABI);
```

We will record this transaction in our state so we can feed it into a progress badge later:

```js
	this.setState({tx});
```

Finally, and crucially, we'll set our state's `counter` key once the contract has been deployed. This makes use an API of `Bond` that we haven't seen yet: `done`. This is somewhat similar to `Bond.then`, except that unlike `.then` which is called _as soon as_ the `Bond` takes a value, `.done` is called only once it has taken a value from which it will never again change.

In the context of contract deployment this means it has been finalised. The object value here contains two keys: `confirmed` (which is the receipt of the transaction which deployed the contract) and `deployed` (which is the final contract object itself). We will record the latter in our state:

```js
	tx.done(s => this.setState({ counter: s.deployed }));
}
```

This leaves us little to do except render a button if the contract hasn't yet been deployed. Our `render` function thus becomes:

```js
return (<div>
	{!!this.state.counter
		? <Counter contract={this.state.counter} />
		: <div>
			<BButton content='Deploy' onClick={this.deploy}/>
			<TransactionProgressLabel value={this.state.tx}/>
		</div>
	}
</div>);
```

To decide whether we have yet deployed a contract, we look at the `counter` item in state. If it coerces to `true` then we know we can go ahead and display the `Counter` component as before. Otherwise, we'll need to deploy the contract and instead display the usual combination of button (with the `onClick` handler) and a progress badge (picking up on `this.state.tx`).

One final thing is to include the contract's byte code. You'll need to either get your own byte code from the compiler (as we did when we deployed the contract in the first place), or alternatively, just use this:

```js
const CounterCode = '\
0x6060604052341561000c57fe5b5b61017d8061001c6000396000f300606060405263fffff\
fff60e060020a6000350416630121b93f811461003757806309eef43e1461004c5780635df8\
13301461007c575bfe5b341561003f57fe5b61004a6004356100a1565b005b341561005457f\
e5b610068600160a060020a036004351661012a565b60408051911515825251908190036020\
0190f35b341561008457fe5b61008f60043561013f565b60408051918252519081900360200\
190f35b600160a060020a03331660009081526001602052604090205460ff16156100c85760\
006000fd5b60008181526020818152604080832080546001908101909155600160a060020a0\
3331680855292819052818420805460ff191690911790555183927f4d99b957a2bc29a30ebd\
96a7be8e68fe50a3c701db28a91436490b7d53870ca491a35b50565b6001602052600090815\
2604090205460ff1681565b600060208190529081526040902054815600a165627a7a723058\
20ef4d0d5cd232790fab92fcd67c633d228600212c1f337d0c68622f19f106c7fc0029';
```

Try it now and you'll see a deploy button waiting:

![image](https://cloud.githubusercontent.com/assets/138296/24910906/88da3b5e-1ec9-11e7-9060-5776f20e89d3.png)

After clicking, it'll prompt you with a transaction to deploy the contract and eventually show you a blank voting tally, ready for you to cast the first vote!

![image](https://cloud.githubusercontent.com/assets/138296/24910910/8ba7f420-1ec9-11e7-95a1-2aa5596709a4.png)

### 3. Consolidating

Since we have the `TransactButton` component, we may as well use it.


First, we won't need to maintain the status of the transaction which is deploying the contract. Remove the `tx: null` mapping in the constructor, so that line now reads:

```js
this.state = { counter: null };
```

Next, the `deploy` function no longer needs to `setState({tx})` since we're not tracking the transaction; remove that line, too. The deploy function *will* however need to return the transaction `Bond`, since `TransactButton` will need it to track; end the function with `return tx`, so it now reads:

```js
deploy () {
	let tx = bonds.deployContract(CounterCode, CounterABI);
	tx.done(s => this.setState({ counter: s.deployed }));
	return tx;
}
```

You can remove the `BButton` and `TransactionProgressLabel` tags and replace them by the single tag:

```jsx
<TransactButton content='Deploy' tx={this.deploy} statusText/>
```

Notice we're passing `this.deploy` in the `tx` prop in lieu of a straight transaction; in case you wish to handle the submission of the transaction yourself, you can pass it a function which does that and will be called when the button is clicked. We're also passing the `statusText` prop which puts a text string in the progress label rather than just an icon.

Finally, don't forget to import the component:

```js
import {InputBond, HashBond, BButton, TransactionProgressLabel, AccountIcon, TransactButton} from 'parity-reactive-ui';
```

And that's it!

### 4. What about Next Time?

This is great for now, but when we reload or return to this page, it will have forgotten the contract. We'll use the browser's local storage to ensure that it persists between sessions.

In the `deploy` function, change the `tx.done()` line to store the contract's address in `window.localStorage`:

```js
tx.done(s => {
	this.setState({ tx: null, counter: s.deployed });
	window.localStorage.counter = s.deployed.address;
});
```

Secondly, in the constructor, change the `this.state =` line to ensure that we recreate the contract at preference from any address stored in local storage; for this we'll use our old friend `bonds.makeContract`:

```js
this.state = { counter: window.localStorage.counter
	? bonds.makeContract(window.localStorage.counter, CounterABI)
	: null };
```

The next time you deploy in that browser session, it will be last (at least until you reset it in the next tutorial!).

### 5. And What about Dave?

So this is great for us, but as soon as we want to give it to someone else to vote, we run into an issue; they have no way of arriving at the vote we deployed. Let's also allow the user to enter the address of a vote instead of forcing them to deploy a new one.

First we'll create a basic `Bond` in the constructor which will hold the address of the contract that the user entered. This goes right after `super()`:

```js
this.addr = new Bond;
```

Next we'll set things up so that the very first value that is placed in it is used (and saved) as our counter contract's address:

```js
this.addr.then(v => {
	window.localStorage.counter = v;
	let counter = bonds.makeContract(v, CounterABI);
	this.setState({ tx: null, counter });
});
```

Now we just have to ensure that the `Bond` only gets set when the user has entered the full address of a valid instance of the `Counter` contract. Luckily the `TextBond` component has a `validator` function allowing us to exactly this.

Before we can realistically be able to detect counter contract instances, we'll need to know what code the contract has on the chain. This is related to, but not the same as, the byte code with which it was initialised. The code hash for my contract (compiled with solidity 0.4.10 with optimisations) is:

```js
const CounterCodeHash = '0x10d2b44a953ecf30231a87c541df5d640b43a30d8ec9a6ed95e411675d8aff42';
```

Yours may be different. To figure it out, use the JS line in any bonds-enabled environment where ADDRESS is the address of a pre-deployed counter contract:

```js
bonds.code(ADDRESS).map(Api.util.sha3).then(console.log)
```

Now to the code. First, we need to import the `Api` object from `@parity/parity.js`:

```js
import {Api} from '@parity/parity.js';
```

After the button in the `render` function, we will add the following:

{% raw %}
```js
<span style={{margin: '2em'}}>OR</span>
<InputBond bond={this.addr} validator={v => v.startsWith('0x') && v.length == 42 && bonds.code(v).map(_ => parity.api.util.sha3(_) == CounterCodeHash) ? v : null}/>
```
{% endraw %}

The first line just splits the button from the text input. The second is our input for counter contract addresses. The `TextBond` we have seen before in the very first tutorial about `Bond`s. We have introduced a custom `validator` prop here, which first makes sure the string form `TextBond` is well-formed and then checks to make sure that the hash of the code at that address is what we would expect from a `Counter` contract. Only then is it valid and does it get set to `this.addr`.

The only thing that is left is to ensure the address of a deployed contract is visible so it can be communicated. Right at the end of the `Counter` render's outer `div`, you can insert this final element:

{% raw %}
```js
<div style={{fontSize: 'small'}}>
	Using contract at {this.props.contract.address}.
</div>
```
{% endraw %}

You can now reload and set your current Counter contract's address like here:

![image](https://cloud.githubusercontent.com/assets/138296/24923831/18de9f82-1ef3-11e7-84d2-a4ee1c5cbe78.png)

To try it out, first clear your local storage (in Chrome open dev console and head to the '127.0.0.1' frame, then enter `delete localStorage.counter`). On reload, you should be presented with the option to deploy or to enter an address.

![image](https://cloud.githubusercontent.com/assets/138296/24924187/369c9ca8-1ef4-11e7-871f-d9bca011377f.png)

Once you complete the address, it will change into the voting window. Hurrah!

Next, up we'll alter the contract a little to require a signed statement prior to voting.

----

[10th Part ⟶](Tutorial-Part-10.md)
