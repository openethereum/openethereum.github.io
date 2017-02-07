# Tutorial Part II

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

The top two lines of this file import our dependency React (which helps us modularise our components and help ensure we have a reactive UI) and our CSS styles.

This is a JSX file. Basically, that means it can handle embedded HTML (well, a dialect of XHTML, to be more exact) when describing how React components should be rendered. If this is all new to you, you might want to [familiarise yourself with the technology](http://www.hackingwithreact.com/read/1/3/introduction-to-jsx). For now, I'll assume you either know or don't care.

The next few lines describe our main "App" component, which is basically the entire page. It is a React component, and so inherits from that; we only describe the basic `render` function in which we return a simple HTML snippet: a `div` with our "Hello world" message inside.

### Introducing Bonds

The first thing we'll do is introduce the oo7 "Bond" library. 