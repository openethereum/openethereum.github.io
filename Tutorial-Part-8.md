---
title: DApp Tutorial
---

**This is the eighth part of the Dapp Tutorial.** We assume you're familiar with the content of the past chapters.

[⟵ 7th Part](Tutorial-Part-7.md) | [9th Part ⟶](Tutorial-Part-9.md)

----

## Events

Events are items emitted by a contract allowing it to communicate to the external world. Usually they notify us of some particular important transition such as the receipt of funds or the change of an owner. In the case of our `Counter` contract, we have a single kind of `event`, called `Voted`, which alerts us to the action of somebody voting.

### 1. What did I vote for?

Let's use this to display a message describing what (if anything) we already voted for. For this, we will need to filter all events to retrieve the event emitted when we voted. Happily, there is a way of doing exactly this and, even better, it returns a `Bond`, so is reactive in the case of new blocks (with new logs) or, worse, a chain reorganisation.

The way we set such a bond up is through calling into the function named after the `event` (i.e. `Voted`) with up to two arguments. The first argument allows us to filter the events we return by any of the `indexed` arguments to the event. The second argument controls which blocks whose transactions' logs are included in the list that we receive. It is extremely important to filter as much as possible as early as possible since it contributes to significant resource usage, particularly so on the light client.

In our case, we won't bother filtering by which blocks we include, but we will filter by the `indexed` argument `who`, asking for it to be exactly equal to our current identity (`bonds.me`). It is reactive, so if we switch to a different identity, everything will update automatically. The snippet we end up with is `this.counter.Voted({ who: bonds.me })`. We will assign this into our object under the name `prevVote` at the end of the constructor:

```js
this.prevVote = this.counter.Voted({ who: bonds.me });
```

Next, we should render it to the display in a message. For this we could create a new React component as we did with `VoteOption`, but it's a bit overkill for the moment. Instead we'll just use our trustly old friend `Rspan`. Inside the `Rspan`, we'll simply `map` the value of `prevVote` into a message telling us of which option we voted, or the empty string if we didn't yet vote.

Since we know each identity (including our own) is limited to at most one vote, we know that the value of `prevVote`'s `Bond` will be an array with either zero or one items, and that any item will contain four keys; the two parameters to the event (`who` and `option`) as well as `event` (which is the name of the event, `"Voted"`) and finally `log`, which is the raw EVM log representing the event. We care only about the `option`, since we already know the value of `who` due to our filter.

Without the prettifying message, our snippet will therefore look something like: `v.length > 0 ? v[0].option : ''`. The only things left are to change the option index into the name (using the `Option` array) and to enclose it in a message. Here's what gets added to the `render` function (I put it immediately before the final `</div>`):

```js
<Rspan>
{this.prevVote.map(v => v.length > 0 ? `Already voted for ${Options[v[0].option]}` : '')}
</Rspan>
```

With that, we can readily see the message in action:

![image](https://cloud.githubusercontent.com/assets/138296/24864358/426e94a0-1e04-11e7-9253-8e3423268892.png)

This account voted green...

![image](https://cloud.githubusercontent.com/assets/138296/24864365/47c02b30-1e04-11e7-88c6-79f5ef31d4f4.png)

...whereas Parity Common voted Red.

![image](https://cloud.githubusercontent.com/assets/138296/24864351/3a4fbb28-1e04-11e7-8879-6f1538af2cb8.png)

This one has not yet cast a vote.

### 2. Chalking 'em up

This is great for figuring out which option we voted for if we only tend to use a single identity, but if we habitually use a few, then it's a bit of a pain to see that information. Nicer would be to display the icons of the our accounts that have voted right next to the row of the graph of the option that they voted. But how would we do this?

The icon itself can be displayed through using another component from `parity-reactive-ui`, namely `AccountIcon`. You'll need to import it along with the progress badge:

```js
import {InputBond, HashBond, BButton, TransactionProgressLabel, AccountIcon} from 'parity-reactive-ui';
```

This gives us a react component `AccountIcon` that accepts props `address` (the address of the account to display), `style` and `className` which can be used to manage the appearance of the component; we'll use it to add a margin and change the size.

In order to figure out which of our accounts voted for which option, we'll need to grab all events involving our accounts voting. Happily, we are able to provide the event filter an array of alternatives of which only one needs to match for any given log. Particularly conveniently, there is a bond containing the array of all of our accounts: `bonds.accounts`. We'll place the new bond in `this.prevVotes`, so the following line is added to our constructor:

```js
this.prevVotes = this.counter.Voted({ who: bonds.accounts });
```

Next up, we will need to partition those events by the option on which they voted. Since we are already looping through the options during render, it makes sense to apply a filter there (`.filter(x => x.option == i)`). Once filtered, we can discard the extended event information and keep only the address: (`.map(x => x.who)`). We will pass this find bond, which represents the array of addresses voting for this option, through to the `VoteOption` component with a new prop called `already`.

The final prop assignment and transformation comes out as:

```js
already={this.prevVotes.map(a => a.filter(x => x.option == i).map(x => x.who))}
```

This should be added to the props of the `VoteOption` tag.

To finsh up, we'll need to actually display them in the row by changing `VoteOption` component. The first thing to do here is add the new prop to the `super` initialiser's `Bond` props list, since we expect it to be a `Bond`:

```js
super(['votes', 'enabled', 'already']);
```

So now we know that `this.state.already` will contain an array of addresses. The final thing to do is to transform this into an array of properly styled `AccountIcon`s. To do this, we simply `.map` the value:

{% raw %}
```js
{this.state.already.map(a => (<AccountIcon
	style={{width: '1.2em', verticalAlign: 'bottom', marginLeft: '1ex'}}
	key={a}
	address={a}
/>))}
```
{% endraw %}

This goes before the final closing `span` tag.

Notice we are aligning the icon at the bottom of the text field (rather than baseline) in order to ensure it appears well centred. All in, we now see this:

![image](https://cloud.githubusercontent.com/assets/138296/24864065/5e3644ae-1e03-11e7-9046-80d58a84bdb3.png)

Since nobody but me has been voting here, it's perhaps no surprise that the number of account icons exactly matches the width of the graph!

Next, we'll look into managing the deployment of contracts.

----

[9th Part ⟶](Tutorial-Part-9.md)
