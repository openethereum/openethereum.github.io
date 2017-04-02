**This is the sixth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 5th Part](https://github.com/paritytech/parity/wiki/Tutorial-Part-V) | [7th Part ⟶](https://github.com/paritytech/parity/wiki/Tutorial-Part-VII)

----

## A New Contract

So far we have inspected with the Parity Registry contract by calling some of its constant functions, but we haven't really explored the realm of contracts in general. In this tutorial, we will create an entirely new contract, deploy it and interact with it.

In Ethereum, contracts are computer programs which live on the blockchain. By sending a transaction to an address of a deployed contract, you cause the contract (or some portion thereof) to execute. Contracts are allowed to store data between successive transactions, allowing you to integrate more or less any logic you can imagine into a trust-free environment.

We will start with a very basic contract. It will count votes over a number of options (which we will just identify with numbers). It will avoid double-counting by allowing each Ethereum identity to vote at most once.

Here is the contract code, written in Solidity:

```
contract Counter {
	event Voted(address indexed _who, uint indexed _option);

	function vote(uint _option) {
		if (hasVoted[msg.sender]) throw;
		votes[_option]++;
		hasVoted[msg.sender] = true;
		Voted(msg.sender, _option);
	}

	mapping (uint => uint) public votes;
	mapping (address => bool) public hasVoted;
}
```

You can compile it within the Parity Wallet by using the "Develop" page in the "Contracts" tab. After compiling it, you'll end up with two things: the EVM initialisation byte-code and the contract's ABI. For now we will assume the contract contract is already deployed: this can be done with Parity Wallet by using the "Deploy" function.

![image](https://cloud.githubusercontent.com/assets/138296/24465379/1a275082-14ad-11e7-88f4-140eb2c3ab74.png)

Above I'm about to deploy the contract using Parity Wallet. After deploying, I'll be given an address for where the contract resides on the blockchain. In my case, it's at `0x7aC77Cb854E064f22E747F40b90FE6D6Bc1e3197` and looks like this:

![image](https://cloud.githubusercontent.com/assets/138296/24465867/014d519a-14af-11e7-83f1-f39fa46a898a.png)

In order to use the contract from our Javascript-based dapp, we'll need its interface. This is given in a piece of JSON known as the ABI definition, and is spat out by the compiler. Here is our contract's ABI definition:

```js
[
	{"constant":false,"inputs":[{"name":"_option","type":"uint256"}],"name":"vote","outputs":[],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"","type":"address"}],"name":"hasVoted","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"","type":"uint256"}],"name":"votes","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},
	{"anonymous":false,"inputs":[{"indexed":true,"name":"_who","type":"address"},{"indexed":true,"name":"_option","type":"uint256"}],"name":"Voted","type":"event"}
]
```

Together with the address of where it is deployed, this will allow us to interact with it.

### Back to Dapp

In order to interact with a deployed contract, we first need to create a local Javascript object which represents that contract. This will let us inspect it (much like we did the registry previously) and also interact in a way that actually changes its state. We have the `makeContract` function in `oo7-parity`, which takes the address and ABI definition and returns the corresponding `Bond`-based contract object.

First we will place our ABI definition in a constant:

```js
const CounterABI = [{"constant":false,/*...*/,"type":"event"}];
```

(I elided most of it for brevity; you'll need to actually put the full JSON object there.)

Within the context of voting, we will dictate that there are three useful options, signifying the options red, green and blue:

```js
const Options = ['Red', 'Green', 'Blue'];
```

Next we'll create a `Counter` object from this ABI and the address where we can find it deployed. Clear out your constructor and ensure that (aside from `super();`) this is the only line there:

```js
this.counter = parity.bonds.makeContract('0x7aC77Cb854E064f22E747F40b90FE6D6Bc1e3197', CounterABI);
```

`this.counter` is a Javascript object that includes four functions corresponding to the four linguistic items in our contract: the `vote` function, the `Voted` event and the two fields `votes` and `hasVoted`.


Our render function will just use three `<div>`s of different width to display the number of votes:

```
render () {
		return (<div>
			{Options.map((n, i) => (<div key={i}>
				<Rspan style={{
					borderRight: this.counter
						.votes(i)
						.map(v => `${1 + v * 10}px black solid`)
				}}>
					<span style={{float: 'left', minWidth: '3em'}}>
						{n}
					</span>
				</Rspan>
			</div>))}
		</div>);
```

The only interesting code there is `this.counter.votes(i)`, which we map into a style string allowing us to generate a black bar of the according length.

If you are using your own address, you won't see much other than the labels. If you're using mine (deployed on the Kovan testnet), then you'll probably see something like:

![image](https://cloud.githubusercontent.com/assets/138296/24588110/807cec58-17c2-11e7-8e08-610f972c63ed.png)

Not exactly great, but it's a start.

### Voting

Next, let's let the user cast their vote. To do this, we'll turn the colour labels into `<a>` tags which when clicked result in a voting transaction being sent.

```js
<a
	style={{float: 'left', minWidth: '3em'}}
	href='#'
	onClick={() => this.counter.vote(i)}>
>
	{n}
</a>
```

So, our `<span>` element changes to an `<a>` tag with the `onClick` property calling this object's function to cast a vote along with the index of the option to vote for. Click one of the three options link and you'll see the Parity Signer asking you whether you really want to sign the transaction.

We're not being particularly forthcoming about the state of the transaction as it goes through the stages of authentication and publication. For this we'll use a prebaked component, `TransactionProgressBadge` from the `parity-reactive-ui.js` package, available on NPM. To install it, you can use:

```sh
npm i --save parity-reactive-ui
```

We can then import the object we're interested in from it:

```js
import {TransactionProgressBadge} from 'parity-reactive-ui';
```

For this to work properly, we will need to store the "current" transaction (which we'll call `tx`) within our state. We'll initialise it to `null` in our constructor:

```js
this.state = { tx: null };
```

Next we'll assign it during the `onClick` action, so the line becomes:

```js
onClick={() => this.setState({tx: this.counter.vote(i)})}>
```

Finally, we'll display the state of the current transaction at the bottom of the page, so we'll need to add this directly before the final `</div>`:

```js
<div style={{marginTop: '1em'}}>
	<TransactionProgressBadge value={this.state.tx}/>
</div>
```

You can now try voting and you'll get a small badge explaining the progress of the action:

![image](https://cloud.githubusercontent.com/assets/138296/24588762/3e436f08-17cf-11e7-9d78-e7e6b528514a.png)

### Splitting things out

Things can easily become a bit cramped when everything happens inside the same component; let's split things out a little. We could do this in a naive way, using another simple React class to represent a vote option and keep all of the code the same. However, since we are splitting into a React class, we have another option open to us.

`ReactiveComponent` is a class provided by `oo7-react` (you need to add it to the import line) in order to minimise the fuss when creating React components that need to handle props whose value may be `Bond`s. By providing the names of any props which may take a `Bond` when calling `super()` in the constructor, the underlying values of those bonds are provided to you under similarly named keys in `this.state`. Everything is fully integrated into the React object's system, so you'll find `render()` gets called whenever the underlying value of any of your prop `Bond`s change. In all, this means you no longer need to fall back on `Bond`-aware elements like `Rspan` and can instead create your own as you need them.

Let's create our first `ReactiveComponent`, which will be a voting option, roughly everything in the `Rspan` currently. After the `class` declaration, we need to inform the `super` of which props we like to support `Bond` values on. At this point, the only bond we're interested in is the number of votes of this option, so we'll ust pass a single-item array of `'votes'`.

For the `render()` function, we'll return something similar to the original `Rspan`, however, since we now have the plain values we can instead use a basic `span` tag. The `borderRight` value is no longer the mapped expression, but rather the direct version of it, using our object's state which has been populated by `ReactiveComponent` for us. We pass in the `vote` and `label` props from the parent context. This leaves us with:

```js
class VoteOption extends ReactiveComponent {
	constructor () {
		super(['votes']);
	}
	render () {
		return (<span style={{ borderRight:
			`${1 + this.state.votes * 10}px black solid` }}>
			<a
				style={{float: 'left', minWidth: '3em'}}
				href='#'
				onClick={this.props.vote}>
					{this.props.label}
			</a>
		</span>);
	}
}
```

The task of the parent context is to provide those three props: `label`, `vote` and `votes` (with the last able to be a `Bond` value). All three are trivially copied from the existing code, so our `Options.map` structure now becomes:

```js
{Options.map((n, i) => (<div><VoteOption
	key={i}
	label={n}
	votes={this.counter.votes(i)}
	vote={() => this.setState({tx: this.counter.vote(i)})}
/></div>))}
```

### Disabling

It's not always a valid action to vote. When we have already voted with the current account, or when we are in the middle of voting (perhaps while it is being signed) there is little point allowing the user to vote again. While our contract's logic defends against such mistaken actions having any effect in these situations, it would be nice if the UI were to reflect this by disabling the links.

Happily, having split out our voting into a separate, reactive component, this task is fairly easy. We will introduce a new reactive `enabled` prop into `VoteOption` (so we get `super(['votes', 'enabled']);` in our constructor). When this is `false` it will prevent the link from calling `vote` and will display a `not-allowed` mouse cursor; the `render` function now becomes:

```js
var s = {float: 'left', minWidth: '3em'};
if (!this.state.enabled)
	s.cursor = 'not-allowed';
return (<span style={{ borderRight:
	`${1 + this.state.votes * 10}px black solid` }}>
	<a
		style={s}
		href='#'
		onClick={this.state.enabled && this.props.vote}>
		{this.props.label}
	</a>
</span>);
```

Now we have to craft a `Bond` which evaluates to whether a vote is allowed to be cast. To prepare for this, we'll create a new field in the object `voted`, which is just the bond expressing whether the current user has voted or not. The following line goes at the bottom of the constructor:

```js
this.voted = this.counter.hasVoted(parity.bonds.me);
```

Our `votingEnabled` bond can now easily be defined as, roughly, "user hasn't voted yet and the current voting transaction must either not exist or have failed". This translates to:

```js
var votingEnabled = Bond.all([this.voted, this.state.tx])
	.map(([v, t]) => !v && (!t || !!t.failed));
```

Notice the use of `Bond.all` in order to collect the two input bonds which we need to determine the final value. This line must appear in `render` (or some other function which gets called when the state changes), since `this.state.tx` will change its value outside the constructor.

Finally, the can add the simple props line `enabled={votingEnabled}` to the `VoteOption` tag.

If you try it out, you'll find it does the right thing, with voting disabled when you've already voted or there's one in progress.

You should now have a good idea of how to post transactions via contract state-change functions. Next up, we'll improve the user-experience further by displaying your vote if you've already voted by inspecting and reacting to contract events.

----

[7th Part ⟶](https://github.com/paritytech/parity/wiki/Tutorial-Part-VII)
