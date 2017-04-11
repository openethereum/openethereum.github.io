**This is the ninth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 8th Part](https://github.com/paritytech/parity/wiki/Tutorial-Part-VIII) | [10th Part ⟶](https://github.com/paritytech/parity/wiki/Tutorial-Part-X)

----

## Deploying a Contract

For our penultimate trick, we will introduce the ability to not just use, but also deploy the contract from our fledgling dapp. Deploying a contract is very similar to any other kind of transaction; we use a slightly different API (`parity.bonds.deployContract`) in order to conveniently return us with a `Contract` object rather than just a transaction receipt as with the standard `parity.bonds.post` API.

There is backwards compatibility, so we can use the same `TransactionProgressBadge` in order to display the progress of the deployment transaction as it gets signed and confirmed.

### Split and Prop

Before we can begin in earnest, we will need to refactor some of our existing code. Whereas at the moment, our dapp heads straight into the "voting" mode we actually want to create a new mode of operation where we are deploying the underlying contract. To do this, we will rename the class `App` to `Counter` (and remove the `export` functionality) and introduce a new exported class `App`.

The new class `App` will, for now, just `render()` a `Counter` object. However, the one key change we'll make here is to pass in the `Counter` contract as a prop named `contract`. This has a two repercussions sicne we're working in React. Firstly, we should move the definitions of `this.voted`, `this.prevVote` and `this.prevVotes` from the constructor into `componentWillReceiveProps` and `componentWillMount`, so that they respond to changes in the prop value.

Secondly, rather than storing the `Counter` contract object in `App`'s '`this.counter` we'll place it in the more correct `this.state.counter`, so that should it change (which it will, but not quite yet), it is reflected in the browser through forcing a new `render()`.

Here's the final code for class `App`:

```js
export class App extends React.Component {
	constructor () {
		super();
		this.state = { counter: parity.bonds.makeContract('0x7aC77Cb854E064f22E747F40b90FE6D6Bc1e3197', CounterABI) };
	}
	render () {
		return (<div>
			<Counter contract={this.state.counter} />
		</div>);
	}
}
```

While the `render` of the function of the new `Counter` class is equivalent to the same function in the original `App` class, the `constructor` portion of the new `Counter` class now reads:

```js
constructor() {
	super();
	this.state = { tx: null };
}
componentWillMount () { this.componentWillReceiveProps(this.props); }
componentWillReceiveProps (props) {
	this.voted = this.props.contract.hasVoted(parity.bonds.me);
	this.prevVote = this.props.contract.Voted({ who: parity.bonds.me });
	this.prevVotes = this.props.contract.Voted({ who: parity.bonds.accounts });
}
```

See how the bonds' setup have been move out of the constructor (inert to prop changes) into the React APIs functions which execute along with prop changes.

With these changes, the dapp will remain essentially the same, however the ground is now prepared for introducing the deployment mode.

### The Real Deal

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
function deploy () {
	let tx = parity.bonds.deployContract(CounterCode, CounterABI);
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
			<RRaisedButton label='Deploy' onClick={this.deploy}/>
			<TransactionProgressBadge value={this.state.tx}/>
		</div>
	}
</div>);
```

To decide whether we have yet deployed a contract, we look at the `counter` item in state. If it coerces to `true` then we know we can go ahead and display the `Counter` component as before. Otherwise, we'll need to deploy the contract and instead display the usual combination of button (with the `onClick` handler) and a progress badge (picking up on `this.state.tx`).

One last thing, is to include the contract's byte code. You'll need to either get your own byte code from the compiler (as we did when we deployed the contract in the first place), or alternatively, just use this:

```js
const CounterCode = '\ 0x6060604052341561000c57fe5b5b61017d8061001c6000396000f300606060405263fffff\
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

### What about Next Time?

This is great for now, but when we reload or return to this page, it will have forgotten the contract. We'll use the browser's local storage to ensure that it persists between sessions.

In the `deploy` function, change the `tx.done()` line to store the contract's address in `window.localStorage`:

```js
tx.done(s => {
	this.setState({ counter: s.deployed });
	window.localStorage.counter = s.deployed.address;
});
```

Secondly, in the constructor, chgange the `this.state =` line to ensure that we recreate the contract at preference from any address stored in local storage; for this we'll use our old friend `bonds.makeContract`:

```js
this.state = { tx: null, counter: window.localStorage.counter
	? parity.bonds.makeContract(window.localStorage.counter, CounterABI)
	: null };
```

And that's it! The next time you deploy in that browser session, it will be last (at least until you reset it in the next tutorial!).

----

[10th Part ⟶](https://github.com/paritytech/parity/wiki/Tutorial-Part-X)
