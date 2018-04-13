---
title: DApp Tutorial
---

**This is the seventh part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 6th Part](Tutorial-Part-6.md) | [8th Part ⟶](Tutorial-Part-8.md)

----

## Interacting

Previously, we created and deployed a contract for voting and made our Dapp use this to display how many votes were given to each of a number of named options. Now, let's let the user cast their vote.

### 1. Voting

To do this, we'll turn the colour labels into `<a>` tags which when clicked result in a voting transaction being sent.

{% raw %}
```js
<a
	style={{float: 'left', minWidth: '3em'}}
	href='#'
	onClick={() => this.counter.vote(i)}
>
	{n}
</a>
```
{% endraw %}

So, our `<span>` element changes to an `<a>` tag with the `onClick` property calling this object's function to cast a vote along with the index of the option to vote for. Click one of the three options link and you'll see the Parity Signer asking you whether you really want to sign the transaction.

We're not being particularly forthcoming about the state of the transaction as it goes through the stages of authentication and publication. For this we'll the pre-baked component `TransactionProgressLabel` from earlier. Don't forget to import it:

```js
import {InputBond, HashBond, BButton, TransactionProgressLabel} from 'parity-reactive-ui';
```

For this to work properly, we will need to store the "current" transaction (which we'll call `tx`) within our state. We'll initialise it to `null` in our constructor:

```js
this.state = { tx: null };
```

Next we'll assign it during the `onClick` action, so the line becomes:

```js
onClick={() => this.setState({tx: this.counter.vote(i)})}
```

Finally, we'll display the state of the current transaction at the bottom of the page, so we'll need to add this directly before the final `</div>`:

{% raw %}
```js
<div style={{marginTop: '1em'}}>
	<TransactionProgressLabel value={this.state.tx}/>
</div>
```
{% endraw %}

You can now try voting and you'll get a small badge explaining the progress of the action:

![image](https://cloud.githubusercontent.com/assets/138296/24588762/3e436f08-17cf-11e7-9d78-e7e6b528514a.png)

### 2. Splitting things out

Things can easily become a bit cramped when everything happens inside the same component; let's split things out a little. We could do this in a naive way, using another simple React class to represent a vote option and keep all of the code the same. However, since we are splitting into a React class, we have another option open to us.

`ReactiveComponent` is a class provided by `oo7-react` (you need to add it to the import line) in order to minimise the fuss when creating React components that need to handle props whose value may be `Bond`s. By providing the names of any props which may take a `Bond` when calling `super()` in the constructor, the underlying values of those bonds are provided to you under similarly named keys in `this.state`. Everything is fully integrated into the React object's system, so you'll find `render()` gets called whenever the underlying value of any of your prop `Bond`s change. In all, this means you no longer need to fall back on `Bond`-aware elements like `Rspan` and can instead create your own as you need them.

Let's create our first `ReactiveComponent`, which will be a voting option, roughly everything in the `Rspan` currently. After the `class` declaration, we need to inform the `super` of which props we like to support `Bond` values on. At this point, the only bond we're interested in is the number of votes of this option, so we'll just pass a single-item array of `'votes'`.

For rendering, rather than using the standard React `render`, we'll instead use the `ReactiveComponent`'s `readyRender`. This ensures that it will not be called until all `Bond` values are well-defined. Inside it, we'll return something similar to the original `Rspan`, however, since we now have the plain values we can instead use a basic `span` tag. The `borderLeft` value is no longer the mapped expression, but rather the direct version of it, using our object's state which has been populated by `ReactiveComponent` for us. We pass in the `vote` and `label` props from the parent context. This leaves us with:

{% raw %}
```js
class VoteOption extends ReactiveComponent {
	constructor () {
		super(['votes']);
	}
	readyRender () {
		return (<span style={{ borderLeft:
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
{% endraw %}

The task of the parent context is to provide those three props: `label`, `vote` and `votes` (with the last able to be a `Bond` value). All three are trivially copied from the existing code, so our `Options.map` structure now becomes:

```js
{Options.map((n, i) => (<div key={i}><VoteOption
	label={n}
	votes={this.counter.votes(i)}
	vote={() => this.setState({tx: this.counter.vote(i)})}
/></div>))}
```

Don't forget to import `ReactiveComponent`:

```js
import {Rspan, Rimg, ReactiveComponent} from 'oo7-react';
```

### 3. Disabling

It's not always a valid action to vote. When we have already voted with the current account, or when we are in the middle of voting (perhaps while it is being signed) there is little point allowing the user to vote again. While our contract's logic defends against such mistaken actions having any effect in these situations, it would be nice if the UI were to reflect this by disabling the links.

Happily, having split out our voting into a separate, reactive component, this task is fairly easy. We will introduce a new reactive `enabled` prop into `VoteOption` (so we get `super(['votes', 'enabled']);` in our constructor). When this is `false` it will prevent the link from calling `vote` and will display a `not-allowed` mouse cursor; the `readyRender` function now becomes:

{% raw %}
```js
var s = {float: 'left', minWidth: '3em'};
if (!this.state.enabled)
	s.cursor = 'not-allowed';
return (<span style={{ borderLeft:
	`${1 + this.state.votes * 10}px black solid` }}>
	<a
		style={s}
		href='#'
		onClick={this.state.enabled && this.props.vote}>
		{this.props.label}
	</a>
</span>);
```
{% endraw %}

Now we have to craft a `Bond` which evaluates to whether a vote is allowed to be cast. To prepare for this, we'll create a new field in the object `voted`, which is just the bond expressing whether the current user has voted or not. The following line goes at the bottom of the constructor:

```js
this.voted = this.counter.hasVoted(bonds.me);
```

Our `votingEnabled` bond can now easily be defined as, roughly, "user hasn't voted yet and the current voting transaction must either not exist or have failed". This translates to:

```js
var votingEnabled = Bond.all([this.voted, this.state.tx])
	.map(([v, t]) => !v && (!t || !!t.failed));
```

Notice the use of `Bond.all` in order to collect the two input bonds which we need to determine the final value. This line must appear in `render` (or some other function which gets called when the state changes), since `this.state.tx` will change its value outside the constructor.

Finally, we can add the simple props line `enabled={votingEnabled}` to the `VoteOption` tag.

If you try it out, you'll find it does the right thing, with voting disabled when you've already voted or there's one in progress.

You should now have a good idea of how to post transactions via contract state-change functions. Next up, we'll improve the user-experience further by displaying your vote if you've already voted by inspecting and reacting to contract events.

----

[8th Part ⟶](Tutorial-Part-8.md)
