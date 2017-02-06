Here we will go through the steps to making a simple Dapp. By the end of this, you'll be able to head in to Parity, select your Dapp and see it in action.

### 1. Spawning a new Dapp

Our dapp will use modern JS technologies; NPM, WebPack, React, Material UI, Babel, ES6, JSX and OO7 Bonds. Getting a working set up in Javascript with all of this takes time and patience. We'll cheat and just clone a repo with it all ready. First, if you don't have Git, node.js, NPM or Webpack already installed on your system, get them (`sudo apt-get install git npm && npm i -g webpack` might work on Ubuntu).

Next, clone our conveniently prepared `skeleton` repository:

```
git clone http://github.com/ethcore/skeleton mydapp
```

This will make your a new repo `mydapp` with everything set up and ready to go. We will `cd` in to it and remove the origin repository lest it confuse Git:

```
cd mydapp
git remote rm origin
```

It's liberally licensed (Apache 2.0) so you don't have to worry about open sourcing your own code (though obviously you'll be enlightened and want to do that anyway :-)). You're now free to push it out to your own Git repo, if you decide to create one on Github.

The next stage is to get all the dependencies sorted. NPM is rather handy at looking after this:

```
npm i
```

This should grab and install all that is required. The next thing to do is to build the final web-ready version of the fledgling dapp. We use webpack for this; it'll smash everything together and provide you with a single `index.js` for you in the `dist` path.

```
webpack
```

You now have a basic dapp built. Well done!

### 2. Getting it visible in Parity

While your dapp may well be built, it is not yet easily discoverable. You would have to host it somewhere. This can be done traditionally with a web server but for development, we will use Parity's in-built hosting. To make this work, we will make a symbolic link between our dapp's `dist` directory to Parity's "local" dapp directory.

Parity's directory structure is different depending on your system. For Mac, Parity's local dapp directory sits at `$HOME/Library/Application Support/io.parity.ethereum/dapps`, so you'll need to enter:

```
# For Mac systems
ln -s $PWD/dist $HOME/Library/Application\ Support/io.parity.ethereum/dapps/mydapp
```

For Linux it's `$HOME/.local/share/parity/dapps` - in this case you'd want to enter:

```
# For Linux systems
ln -s $PWD/dist $HOME/.local/share/parity/dapps/mydapp
```

Once you have it linked, you should start (or restart, if already running) Parity and head to the Applications page of Parity Wallet. There you'll see your new dapp: