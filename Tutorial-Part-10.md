---
title: DApp Tutorial
---

**This is the tenth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 9th Part](Tutorial-Part-9.md)

----

## Signing Data

We now come to signing messages. This can be useful if you would like to ensure your user agrees to some particular terms over and above an on-chain transaction, not dissimilar to a click-wrap license.

We will augment our voting contract to require a signed message ("I am eligible to vote!") from the user prior to accepting the vote. To guard the `vote` function with this requirement, we'll introduce the `only_signed` modifier which places the function body into an `if` statement conditional on an additional three parameters (which together constitute the signature) signing the message on behalf of the sender. We will also publish the message to be signed as a `public` field allowing us to get at it when we are in the dapp's JS context.

Here's the final Solidity code:

```
contract Counter {
	event Voted(address indexed who, uint indexed option);

	function vote(uint _option, uint8 _v, bytes32 _r, bytes32 _s)
	    only_signed(msg.sender, _v, _r, _s)
	{
		if (hasVoted[msg.sender]) throw;
		votes[_option]++;
		hasVoted[msg.sender] = true;
		Voted(msg.sender, _option);
	}

	modifier only_signed(address who, uint8 v, bytes32 r, bytes32 s) { if (ecrecover(STATEMENT_HASH, v, r, s) == who) _; else throw; }

	bytes32 constant public STATEMENT_HASH = sha3(STATEMENT);

	string constant public STATEMENT = "\x19Ethereum Signed Message:\n22I am eligible to vote!";

	mapping (uint => uint) public votes;
	mapping (address => bool) public hasVoted;
}
```

As you can see the main difference is that we have a couple of extra public fields (`STATEMENT` is the important one) and three extra parameters to the `vote` function.

Here is the compiled init code, the contract code hash and its ABI spec:

```js
const CounterCode = '\
0x6060604052341561000c57fe5b5b6104a88061001c6000396000f300606060405263ffffffff6\
0e060020a60003504166309eef43e811461004d5780635df813301461007d57806394c21ff31461\
00a2578063b876a628146100c4578063bebc479c146100e5575bfe5b341561005557fe5b6100696\
00160a060020a0360043516610175565b604080519115158252519081900360200190f35b341561\
008557fe5b61009060043561018a565b60408051918252519081900360200190f35b34156100aa5\
7fe5b61009061019c565b60408051918252519081900360200190f35b34156100cc57fe5b6100e3\
60043560ff60243516604435606435610251565b005b34156100ed57fe5b6100f561041c565b604\
08051602080825283518183015283519192839290830191850190808383821561013b575b805182\
52602083111561013b57601f19909201916020918201910161011b565b505050905090810190601\
f1680156101675780820380516001836020036101000a031916815260200191505b509250505060\
405180910390f35b60016020526000908152604090205460ff1681565b600060208190529081526\
04090205481565b6040805160608101825260328082527f19457468657265756d205369676e6564\
204d6573736167653a0a32324920616d602083019081527f20656c696769626c6520746f20766f7\
465210000000000000000000000000000838501529251919282918083835b602083106102215780\
518252601f199092019160209182019101610202565b6001836020036101000a038019825116818\
451168082178552505050505050905001915050604051809103902081565b3383838383600160a0\
60020a03166001606060405190810160405280603281526020017f19457468657265756d2053696\
76e6564204d6573736167653a0a32324920616d81526020017f20656c696769626c6520746f2076\
6f74652100000000000000000000000000008152506040518082805190602001908083835b60208\
3106102ed5780518252601f1990920191602091820191016102ce565b5181516020938403610100\
0a60001901801990921691161790526040805192909401829003822060008381528383018652928\
501839052845190815260ff8c16818301528085018b9052606081018a9052935160808086019750\
919550601f19810194819003909101925090866161da5a03f1151561036857fe5b5050602060405\
10351600160a060020a0316141561040b57600160a060020a033316600090815260016020526040\
90205460ff16156103a75760006000fd5b600088815260208181526040808320805460019081019\
09155600160a060020a03331680855292819052818420805460ff19169091179055518a927f4d99\
b957a2bc29a30ebd96a7be8e68fe50a3c701db28a91436490b7d53870ca491a35b610411565b600\
06000fd5b5b5050505050505050565b606060405190810160405280603281526020017f19457468\
657265756d205369676e6564204d6573736167653a0a32324920616d81526020017f20656c69676\
9626c6520746f20766f7465210000000000000000000000000000815250815600a165627a7a7230\
5820238cb0019a9db164baca03cc247a23f175141a78bb795d73900d12ee471847860029';
const CounterABI = [
	{"constant":true,"inputs":[{"name":"","type":"address"}],"name":"hasVoted","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},
	{"constant":true,"inputs":[{"name":"","type":"uint256"}],"name":"votes","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},
	{"constant":true,"inputs":[],"name":"STATEMENT_HASH","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"},
	{"constant":false,"inputs":[{"name":"_option","type":"uint256"},{"name":"_v","type":"uint8"},{"name":"_r","type":"bytes32"},{"name":"_s","type":"bytes32"}],"name":"vote","outputs":[],"payable":false,"type":"function"},
	{"constant":true,"inputs":[],"name":"STATEMENT","outputs":[{"name":"","type":"string"}],"payable":false,"type":"function"},
	{"anonymous":false,"inputs":[{"indexed":true,"name":"who","type":"address"},{"indexed":true,"name":"option","type":"uint256"}],"name":"Voted","type":"event"}
];
const CounterCodeHash = '0xbd14a8001440940649ecfbd1e064132ebeff4487b8f9c57bea485a90067b1aee';	// TODO
```

With those three lines in place, we're ready to introduce the signing logic.

### Putting Pen to Paper

With Parity dapps, signing is very similar to creating a transaction or deploying a contract. We can use `bonds.sign`; it takes two arguments: the message to be signed and, optionally, the identity (address) that should sign it. The latter defaults to the user's currently selected identity. The function returns a `Bond` which evaluates to the present status of the signing request, ending either in an object with a key called `failed` or (on success) an object with a key of `signed`, whose value is the three elements of the signature concatenated into 65 bytes of data. `done` works properly for this kind of `Bond`, which is convenient, as we'll be relying on it.

Our changes will all take place in the `Counter` class. We'll refactor the voting mechanism so the logic is in a new function, `vote`. Since we'll be calling this from a handler, it makes sense to bind it to the object during the constructor, much as we did with `App`'s `deploy` function. So append this to the constructor function:

```js
this.vote = this.vote.bind(this);
```

Next we'll redirect the vote operation into (what will be) our `vote` function; change the line beginning `vote={...}` so that it now calls `this.vote` rather than attempting to vote directly there. Don't forget we'll need which option it represents, so pass `i` as a parameter:

```js
vote={() => this.vote(i)}
```

Finally, we need to introduce the new voting function:

```
vote (option) {
```

Here, we must first have the user sign the required statement and then pass in this signature information if (and only if) the user went through with the signature.

First off, the message:

```js
	let message = this.props.contract.STATEMENT().map(removeSigningPrefix);
```

This retrieves the `STATEMENT` field from the contract (the message which must be signed) and shaves off the text which is prepended automatically (and mandatorily) by our client. `removeSigningPrefix` comes from `oo7-parity.js`, so ensure you import that extra symbol. `message` will now be the string "I am eligible to vote!".

Next, we initiate the signing operation. We call `bonds.sign` with our message. When `done`, we should check whether the user actually finished the operation (i.e. the final value is an object containing `signed`):

```
	bonds.sign(message).done(s => {
		if (s.signed) {
```

If the user did approve it to be signed, then we just execute similarly to before, calling `vote` on the contract. The main difference is that now we will pass the signature in, appending the three elements in the signature array as arguments into `vote` (i.e. `...s.signed`):

```
			this.setState({tx: this.props.contract.vote(option, ...s.signed)});
		}
	});
}
```

And that's it. If you try now (don't forget to `delete window.localStorage.counter` first), you'll find when you try to vote then the client gives you a statement to sign which you must approve. If you approve it, the operation will continue just as before.

![image](https://cloud.githubusercontent.com/assets/138296/24996522/794af884-2033-11e7-8762-9e4e30d60967.png)

### The End?

And that concludes this initial series of Dapp tutorials. Over these ten lessons, you've learned how to get started in building a dapp, you've got the basics of `Bond`s, how they integrate with parity and React. You've learnt how to inspect aspects of the blockchain and display them. You've learnt how to send transactions and track their progress. You've learnt how to deploy a contract both manually and programatically. You've learnt how to interact with contracts by calling inspector functions, by tracking events and through transactions which mutate their state. And finally you're learnt how to sign messages.

Now is the time to push forward. You'll find comprehensive documentation for the Parity `Bond` interface [on the wiki](oo7-Parity-Reference.md), as well as other resources that can help you improve and get productive.

Good luck!
