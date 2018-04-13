---
title: DApp Tutorial
---

**This is the fifth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 4th Part](Tutorial-Part-4.md) | [6th Part ⟶](Tutorial-Part-6.md)

----

## Posting Transactions

So far we've learned how to inspect much of the information associated with the blockchain either through calling into contracts or through Parity's own APIs. We have seen how the Bond framework have made those tasks very easy. However, we have not yet had any impact on the blockchain's state---we have not yet sent a transaction.

Sending a transaction in Ethereum is a multi-stage process. The transaction is initially submitted to Parity for signing. During this stage, Parity it free to consult the user to ensure that they authorise this transaction to happen. While in some instances this may be instantaneous, this can also be a long process taking upwards of 60 seconds: depending on the type of account, password or passwords may need to be entered, a 2-factor authentication process may need to occur or the secret key required for signing may be on a different device.

The authorisation stage ends when either the user has decided to approve the transaction (and done all necessary steps to physically create the signature) or when the user has refused the request. Assuming they approved the transaction, it must now be sent to the network for finalisation (also known as "mining" or "confirming"). This is the process of burying the transaction into the blockchain such that it can be considered final. Different consensus mechanisms have different means of determining finality. E.g. proof-of-work chains can never achieve true finality and rather rely on the increasingly small chance than an attacker can secretly create a longer chain buoyed by greater proof of "work". Conversely, some consensus algorithms are able to provide "finality" after only a single block.

Once the first block has been received in which the transaction appears, we consider the transaction to be "confirmed"; successive blocks may reveal further confirmations. For the present purposes, we consider only the first confirmation to be "important".

To create a new transaction in Parity, we require only a single new API: the `bonds.post` function. The function returns a type of `Bond`. Constructing it creates a new transaction for the user to approve and which can be pushed out on to the network. The value to which the Bond evaluates reflects the ongoing status of the transaction as it moves through the process of getting finalised. It is always an object with a single key/value. It can be:

- `{"estimating": null}` The amount of gas required for this transaction to execute is being determined.
- `{"estimated": value}` The amount of gas required for this transaction to execute has been determined as `value`; the user is about to be asked.
- `{"requested": id}` The user has been asked for approval of the transaction; `id` is the (numeric) identity of the request.
- `{"signed": hash}` The user has approved the transaction and the transaction has been signed to form a final transaction hash of `hash`. It is now ready to be sent to the network for inclusion in a new block.
- `{"confirmed": receipt}` The transaction has been confirmed into a block. The receipt of the transaction is provided as `receipt`, giving information concerning its operation, including any logs.
- `{"failed": error}` The transaction has failed. Generally this is because the user did not approve the transaction or otherwise signing could not take place. `error` is a string which contains any details regarding the situation.

Creating a new transaction is a simple affair. The function of `bonds.post` takes a single argument: an object describing the transaction. There are several keys it may contain:

- `to` The recipient of the transaction; undefined or `null` indicates this is a contract-creation transaction.
- `from` The sender of the transaction; must be an account which the user controls. Will default to `bonds.me`.
- `value` The amount of ether to send to the recipient or endow any created contract.
- `condition` A condition on which to predicate the distribution, but not the approval/signing, of the transaction. This is an object which contains one of two keys: `block` (which dictates the minimum block number before distribution) and `time` (the same but for block timestamp).
- `gas` The amount of gas to supply with the transaction. By default, it will attempt to estimate the amount of gas to supply. Such that the transaction succeeds without exception/error.
- `gasPrice` The price (in Wei) per unit of gas. Will default to something sensible (in Parity's case, this is the median of the distribution formed from the last several hundred transactions).
- `nonce` The sender nonce of the transaction. Will default to the latest known value.

### 1. Giveth to gavofyork

Let's start simple. A button to give 100 Finney (around $10 at current prices) to me. Since we want to include a button, we'll need the appropriate import for that:

```jsx
import {InputBond, HashBond, BButton} from 'parity-reactive-ui';
```

Next, in our class, we'll maintain the address of 'gavofyork' for efficiency and brevity:

```jsx
constructor() {
	super();
	this.gavofyork = bonds.registry.lookupAddress('gavofyork', 'A');
}
```

There should be nothing surprising there.

Next for the HTML, we'll have two elements: the balance of our default account (so that we can watch it go down once the payment goes out) and the button to make a payment:

```jsx
<div>
	My balance: <Rspan>
		{bonds.balance(bonds.me).map(formatBalance)}
	</Rspan>
	<br />
	<BButton
		content='Give gavofyork 100 Finney'
		onClick={() => bonds.post({to: this.gavofyork, value: 100 * 1e15})}
	/>
</div>
```

The balance is nothing that we haven't seen before. The button's `onClick` prop creates our new transaction. We wish to have the user send `gavofyork` (whose "current" address is expressed by `this.gavofyork`) 100 Finney (one Finney being equivalent to 10**15 Wei). The `bonds.post` creates our transaction; our given options of `to` and `value` reflect the attributes we wish our transaction to take.

Refresh your browser and click the button! You'll see the signer window pop up asking you for approval (and maybe a password) to send the funds.

![image](https://cloud.githubusercontent.com/assets/138296/22750413/badb3b72-edfe-11e6-810b-9bcdb47a20a8.png)

Once done, give it a few seconds and you should see you balance reduce by around 0.1 Ether.

### 2. Giveth to anyone

Let's quickly alter the dapp so we can accept any name when we give our 100 Finney. Ensure you have the utility function `isNullData`:

```jsx
import {bonds, formatBalance, isNullData} from 'oo7-parity';
```

Change the constructor so we have a bond for the recipient's name:

```jsx
this.name = new Bond;
this.recipient = bonds.registry.lookupAddress(this.name, 'A');
```

And the part of your `render()` HTML that follows the `br` element to:

```jsx
<InputBond bond={this.name} placeholder='Name of recipient' />
<BButton
	content={this.name.map(n => `Give ${n} 100 Finney`)}
	disabled={this.recipient.map(isNullData)}
	onClick={() => bonds.post({to: this.recipient, value: 100 * 1e15})}
/>
```

This should be fairly clear from the previous tutorial; note that because we're using the Bond-enabled "reactive" variant of `RaisedButton`, our `disabled` and `label` props can be `Bonds`. The end result is to get a button which is only enabled when the name of the recipient is valid:

![image](https://cloud.githubusercontent.com/assets/138296/22752491/b99eb7e0-ee06-11e6-85c7-c11c8a2b99c1.png)

Jutta didn't yet register...

![image](https://cloud.githubusercontent.com/assets/138296/22752481/acdecc3e-ee06-11e6-8e74-600cfe813521.png)

...however Nicholas did.

### 3. Track the transaction

Seeing nothing for those several seconds is a little disconcerting. It would be nicer to actually track the state of the transaction so that we know everything is in order. This is fairly simple since we already have the information we need in the `Bond` which is given by `bonds.post`. The problem is that we're discarding it; in fact we need to keep it around and use it as a reactive display element.

First, we'll introduce some state to our React class. Place this at the bottom of the `constructor`:

```
this.state = { current: null };
```

Next, we'll introduce a function which looks after the creation of the new transaction and its assignment into the state. Enter this new function in the class:

```
give () {
	this.setState({
		current: bonds.post({
			to: this.recipient,
			value: 100 * 1e15
		})
	})
}
```

Next we'll use that function rather than calling the `bonds.post` directly. the `onClick` line becomes:

```
onClick={this.give.bind(this)}
```

Finally, we'll actually display the transaction status in an `Rspan` element. Add this extra line to the end of the element's HTML, right before the final `</div>`:

```
<Rspan>{this.state.current && this.state.current.map(JSON.stringify)}</Rspan>
```

And that's it. Once you click the "send" button, you'll see the "current" transaction changes to `{"request":...}`:

![image](https://cloud.githubusercontent.com/assets/138296/24324260/8d166a56-1183-11e7-8c5f-98db20bfe232.png)

The value of this `request` key is simply the internal identifier given to this particular request. Approving the transaction will lead to it being `{"signed":...}`:

![image](https://cloud.githubusercontent.com/assets/138296/24324265/a612047a-1183-11e7-9536-83324ad97465.png)

The value of the `signed` key here is the hash of the transaction, its unique identifier which stays with it throughout. Under normal circumstances, it will be published to the network and eventually finalised into a block. And after it has been finalised by the network, `this.state.current` will mutate to `{"confirmed":...}`:

![image](https://cloud.githubusercontent.com/assets/138296/24324263/9f3918fa-1183-11e7-8cbd-d04527addcd1.png)

The value of the `confirmed` key is the receipt of the transaction which gives you a lot of information on its specific execution, including what gas it used and logs/events left by it.

If you reject the transaction rather than approving it, you'll see that reflected in the value of `this.state.current`, too:

![image](https://cloud.githubusercontent.com/assets/138296/24324268/b1a21bcc-1183-11e7-9154-5d2a90c69334.png)

### 4. Consolidate

While working with the JSON is fairly easy, it's hardly pretty. There are existing Bond-oriented reactive components that can do this for you, such as `TransactionProgressLabel` found in `parity-reactive-ui` module. Let's use it.

Start by importing the component:

```js
import {InputBond, HashBond, BButton, TransactionProgressLabel} from 'parity-reactive-ui';
```

And then just replace your bottom `Rspan` line with that component, feeding in the current transaction as its `value` prop:

```jsx
<TransactionProgressLabel value={this.state.current}/>
```

You'll see a pretty semantic-ui badge rather than ugly JSON.

The same module also provides a button component which merges all of this together into one: `TransactButton`. If we use that we can remove much of our logic. First remove the `give` function and the `this.state =` line in the constructor.

Next, remove everything below the `InputBond` until the final `</div>` and replace it with:

{% raw %}
```jsx
<TransactButton
	content={this.name.map(n => `Give ${n} 100 Finney`)}
	disabled={this.recipient.map(isNullData)}
	tx={{
		to: this.recipient,
		value: 100 * 1e15
	}}
/>
```
{% endraw %}

Finally, don't forget to import the component:

```js
import {InputBond, HashBond, BButton, TransactButton} from 'parity-reactive-ui';
```

You can configure the button to use whatever colour scheme you prefer, but that's out of scope for the present tutorial.

----

[6th Part ⟶](Tutorial-Part-6.md)
