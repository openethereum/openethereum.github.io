Now we have our basic dapp harness, we can start introducing useful functionality. Without too much ado, let's get started. Head in to `src/client/scripts/app.jsx`. You'll see our basic file:

```jsx
import React from 'react';
import styles from "../style.css";

export class App extends React.Component {
	render() {
		return (
			<div>Hello world!</div>
		);
	}
}
```

This is a JSX file. Basically, that means it can handle embedded HTML (well, a dialect of XHTML, to be more exact) when describing how React components should be rendered. If this is all new to you, you might want to [familiarise yourself with the technology](http://www.hackingwithreact.com/read/1/3/introduction-to-jsx). For now, I'll assume you either know or don't care.

### Your first Bond

The first thing we'll do is introduce the oo7 library. This introduces into Javascript the notion of reactive values known as "bonds". Reactive values are like normal variables, except that changes in them may be subscribed to with arbitrary code. They can be `map`ped and composed into arbitrarily complex expressions.

Our first example will just demonstrate how Bonds can introduce effortless reactivity by dynamically replicating the contents of an editable text field into a `span`.

First, ensure the appropriate libraries are introduced, by placing two lines at the top of the file:

```jsx
import {Bond} from 'oo7';
import {TextBond, Rspan} from 'oo7-react';
```

Next, we need to introduce a new Bond. This will represent the current contents of a text field. We will initialise it in our class's constructor. Insert these lines directly below the first line of the class declaration:

```jsx
constructor() {
	super();
	this.bond = new Bond;
}
```

Next we need to create the text entry field and the span element (in which the text field's contents will be reflected). We will use a version of Material UI's `TextField` element which has been specially modified to write propagate the value into a named `Bond`. This is called `TextBond`. Similarly, for the `span`, we'll use a special "reactive" version of the `span` element which is able to accept `Bond`s as children and values of certain properties; this is known as `Rspan`. Both are already imported from the oo7-react library.

Change the `<div>Hello world</div>` line to:

```jsx
<div>
<TextBond bond={this.bond} floatingLabelText="Go ahead and type some text"/>
<Rspan>{this.bond}</Rspan>
</div>
```

As you see there's not all that much here. We just tell the text field input `TextBond` to place its value into `this.bond` and conversely tell `Rspan` to display that value.

Ensure your dapp is continuously rebuilt by running webpack in a terminal:

```
webpack --watch
```

Reloading our dapp page in the Parity Wallet will give a simple form; client the text field and type something. Whatever you type, you will see it reflected in the `span` element next door:

![image](https://cloud.githubusercontent.com/assets/138296/22694357/e9eae790-ed14-11e6-898b-932b56847a18.png)

### Transforming Bonds

Bonds don't just have to pass on data; they can also represent transformations on the data. One example of a transform on text would be simple upper-casing. A function to uppercase text would be `_ => _.toUpperCase()`. We can `map` our Bond with this function, making the `span` display the upper-case of whatever it is that we enter into the field:

```jsx
<Rspan>{this.bond.map(t => t.toUpperCase())}</Rspan>
```

Reload and play around:

![image](https://cloud.githubusercontent.com/assets/138296/22694526/9f1bf442-ed15-11e6-9e46-f3752f479b76.png)

### Reusing Bonds

Right now we just have a single "user" of our `this.bond`, but actually `Bond`s can be used and reused as much as you want. Let's use the bond to create a style for our span in order to introduce colour depending on the text we have entered. We will give our `span` a red colour if we have entered a simple number, and black in all other cases.

Replace your `Rspan` line with:

```jsx
<Rspan style={this.bond.map(t => t.match(/^[0-9]+$/) ? {color: 'red'} : {color: 'black'})}>
	{this.bond.map(t => t.toUpperCase())}
</Rspan>
```

The code is fairly simple: we `map` our `this.bond` to one of `{color: 'red'}` or `{color: 'black'}` depending on whether it matches the regular expression `/^[0-9]+$/`.

Our bond is now being used twice in two different mappings, but all works as you would expect:

![image](https://cloud.githubusercontent.com/assets/138296/22694918/fa77761c-ed16-11e6-9d18-7431c79eceb3.png)

While the above might have been clear _enough_, the astute reader might have wondered if perhaps there was a way of DRYing the `{color: ...}` changing this:

```
this.bond.map(t => t.match(/^[0-9]+$/) ? {color: 'red'} : {color: 'black'})
```

...into this:

```
{color: this.bond.map(t => t.match(/^[0-9]+$/) ? 'red' : 'black')}
```

The two are rather different, of course. Whereas the former is undeniably a `Bond`, the latter is a simple Object which happens to have a `Bond` as one of its values. In fact, the latter does work. For convenience, reactive values are able to be recognised not just directly, but also when they are within Arrays or the values of Object fields. For efficiency, this only work up to one level deep. Any which are further into the object structure will be completely ignored.

### Multiple Bonds

So far we have only used a single `Bond` for marking up our `span`. What if we want to use several? That works, too.

We could initialise another `Bond` in the constructor, make another `TextBond` input field and use them both in the `span` contents, but that would be a bit samey. Instead, we'll use one of the built-in `Bond`s: the `TimeBond`.

To begin, import the `TimeBond` and `TransformBond` objects alongside `Bond`:

```jsx
import {Bond, TimeBond} from 'oo7';
```

Then introduce it in the constructor:

```jsx
constructor() {
	super();
	this.bond = new Bond;
	this.time = new TimeBond;
}
```

Finally, let's use it in our span. There are several ways of combining the values of multiple `Bond`s into a single expression. For now we'll use the simplest: `Bond.all`. This function allows you to provide a number of expressions and will evaluate to an array of all such expressions. This means that `Bond.all(this.bond, this.time)` will be a _composite_ `Bond`, whose value is a JS array, the first item being the contents of our text field and the second being the time (as a number). Try it. Replace the `<Rspan>` with this new one:

```jsx
<Rspan style={{ color: this.bond.map(t => t.match(/^[0-9]+$/) ? 'red' : 'black') }}>
	{Bond.all([this.bond, this.time])}
</Rspan>
```

Type an appropriate message and you'll end up with:

![image](https://cloud.githubusercontent.com/assets/138296/22697591/e779b292-ed1f-11e6-8beb-2ff654e6ac02.png)

It will automatically update as the time changes (since time changes infinitely fast, a speed which would be expensive to try to match, we take a pragmatic solution and only update the `TimeBond` once per second).

We can make it slightly nicer by first formatting the time:

```jsx
<Rspan style={{ color: this.bond.map(t => t.match(/^[0-9]+$/) ? 'red' : 'black') }}>
	{Bond.all([this.bond, this.time]).map(([msg, t]) => `${new Date(t)}: ${msg}`)}
</Rspan>
```

![image](https://cloud.githubusercontent.com/assets/138296/22697729/62243e2c-ed20-11e6-931a-1693dd865837.png)

That should have given you a good introduction to the concept of the `Bond` and how it can be used within simple React objects. Next up we'll look at the Parity Universal Bond API.
