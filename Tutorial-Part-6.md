---
title: DApp Tutorial
---

**This is the sixth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 5th Part](Tutorial-Part-5.md) | [7th Part ⟶](Tutorial-Part-7.md)

----

## A New Contract

So far we have inspected with the Parity Registry contract by calling some of its constant functions, but we haven't really explored the realm of contracts in general. In this tutorial, we will create an entirely new contract, deploy it and interact with it.

In Ethereum, contracts are computer programs which live on the blockchain. By sending a transaction to an address of a deployed contract, you cause the contract (or some portion thereof) to execute. Contracts are allowed to store data between successive transactions, allowing you to integrate more or less any logic you can imagine into a trust-free environment.

We will start with a very basic contract. It will count votes over a number of options (which we will just identify with numbers). It will avoid double-counting by allowing each Ethereum identity to vote at most once.

Here is the contract code, written in Solidity:

```
contract Counter {
	event Voted(address indexed who, uint indexed option);

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

You can compile it within the Parity Wallet by using the "Develop" page in the "Contracts" tab. After compiling it, you'll end up with two things: the EVM initialisation byte-code and the contract's ABI. For now we will assume the contract is already deployed: this can be done with Parity Wallet by using the "Deploy" function.

![image](https://cloud.githubusercontent.com/assets/138296/24465379/1a275082-14ad-11e7-88f4-140eb2c3ab74.png)

Above I'm about to deploy the contract using Parity Wallet. After deploying, I'll be given an address for where the contract resides on the blockchain. In my case, it's at `0x7aC77Cb854E064f22E747F40b90FE6D6Bc1e3197` and looks like this:

![image](https://cloud.githubusercontent.com/assets/138296/24465867/014d519a-14af-11e7-83f1-f39fa46a898a.png)

In order to use the contract from our Javascript-based dapp, we'll need its interface. This is given in a piece of JSON known as the ABI definition, and is spat out by the compiler. Here is our contract's ABI definition:

```js
[
	{"constant":false,"inputs":[{"name":"_option","type":"uint256"}],"name":"vote","outputs":[],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"","type":"address"}],"name":"hasVoted","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"","type":"uint256"}],"name":"votes","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},
	{"anonymous":false,"inputs":[{"indexed":true,"name":"who","type":"address"},{"indexed":true,"name":"option","type":"uint256"}],"name":"Voted","type":"event"}
]
```

Together with the address of where it is deployed, this will allow us to interact with it.

### 1. Back to Dapp

In order to interact with a deployed contract, we first need to create a local Javascript object which represents that contract. This will let us inspect it (much like we did the registry previously) and also interact in a way that actually changes its state. We have the `bonds.makeContract` function in `bonds`, which takes the address and ABI definition and returns the corresponding `Bond`-based contract object.

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
this.counter = bonds.makeContract('0x7aC77Cb854E064f22E747F40b90FE6D6Bc1e3197', CounterABI);
```

`this.counter` is a Javascript object that includes four functions corresponding to the four linguistic items in our contract: the `vote` function, the `Voted` event and the two fields `votes` and `hasVoted`.


Our render function will just use three `<div>`s of different width to display the number of votes:

{% raw %}
```
render () {
	return (<div>
		{Options.map((n, i) => (<div key={i}>
			<Rspan style={{
				borderLeft: this.counter
					.votes(i)
					.map(v => `${1 + v * 10}px black solid`)
			}}>
				<span style={{float: 'left', minWidth: '3em'}}>
					{n}
				</span>
			</Rspan>
		</div>))}
	</div>);
}
```
{% endraw %}

The only interesting code there is `this.counter.votes(i)`, which we map into a style string allowing us to generate a black bar of the according length.

If you are using your own address, you won't see much other than the labels. If you're using mine (deployed on the Kovan testnet), then you'll probably see something like:

![image](https://cloud.githubusercontent.com/assets/138296/24588110/807cec58-17c2-11e7-8e08-610f972c63ed.png)

Not exactly great, but it's a start. In the next part we'll implement the action of voting.

----

[7th Part ⟶](Tutorial-Part-7.md)
