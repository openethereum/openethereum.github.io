---
title: Deploying Dapps to the Parity Wallet
---

DApps are an exciting way to showcase a contract or a series of contracts with a simple interface that allows users to interact with it in a secure way. This guide will provide a step-by-step guide for developers to develop dapps for their contracts and integrate these into Parity Wallet.

Parity Wallet supports two types of dapps - one locally hosted (ideal for dapps still in development, or those dapps that are private) and network dapps (ideal for dapps applicable to a global community.)


## Local DApps

Local dapps allow the developer to get comfortable with the required interfaces, structure and requirements of dapps before making it available to the global community. As such any dapps deployed locally (either tied to the live or test network - or both), it only available on the local machine. This typically would be the first step to developing a dapp, once satisfied, the developer can move the dapp to the network if so required.

### Creating the DApp Structure

The first step is to generate your dapp and directory structure inside the

    ~/.local/share/io.parity.ethereum/dapps/

folder. Any folders inside `dapps` will get recognised and loaded as a dapp provided that the basic structure is in place. The structure can either be created manually, using the [skeleton dapp](https://github.com/paritytech/skeleton/), or via [chevdor's dapp generator](https://github.com/chevdor/generator-ethdapp), the latter supporting both web3 and parity.js apps.

The rest of the section will walk through the required files, as if assuming that no generator was used - in the case of a generator, it still would be useful to understand the various components.

#### manifest.json

This is the description for the dapp, containing the app id, name, author, description and version. A sample is given below:

    {
        "id": "myfirstdapp",
        "name": "Cool DApp",
        "description": "This is just a toy dapp with almost no functionality",
        "version": "0.0.1",
        "author": "Joe Bloggs",
        "iconUrl": "images/title.png"
    }

#### index.html

The app entry point. The built-in Parity server will automatically serve this page when the dapp is accessed. A skeleton sample is given below:

    <!doctype html>
    <html lang="us">
      <head>
        <meta charset="utf-8">
        <title>Cool dapp</title>
        <link href="app.css" rel='stylesheet' type='text/css'>
        <script src="/parity-utils/parity.js"></script>
      </head>
      <body>
        <div id="container"></div>
        <script src="app.js"></script>
      </body>
    </html>

In the above html, `app.css` will be your app-specific CSS styles, `/parity-utils/parity.js` loads the parity.js APIs (pre-configured, `/parity-utils/web3.js` would use the web3 APIs instead) and `app.js` will contain the specific app logic.

#### app.css

A very simple css file could contain the following -

    #container {
      margin: 1em;
      font-size: 1.5em;
      background: #eee;
      border-radius: 5px;
      padding: 1.5em;
    }

#### app.js

A very simple application (displaying the blockNumber, parity.js APIs) would be the following

    window.parity.api.subscribe('eth_blockNumber', function (error, blockNumber) {
      if (error) {
        console.log('error', error);
        return;
      }

      document.getElementById('container').innerHTML = blockNumber.toFormat(0);
    });

## Network DApps

Once the developer is satisfied that the local dapp operates as intended and wants to take the next step to deploy the dapp to the network. For this Parity Wallet includes a DApp Registry application to register the manifest, image and content.

- It is recommended to do the deployment on the test network first.
- Register the dapp to receive a new unique network id.
- Generate the correct content hashes [via the GitHubHint dapp](Parity-Github-Hint) (GHH).
- For manifests and files, the "File Link" is to be used for the content hash in the GHH dapp.
- For the actual dapp content bundle the "Content Link" option should be used in the GHH dapp.
- Edit the dapp [in the DApp Registry](Parity-dapp-registry) and supply the image, manifest and content hashes as generated in the previous steps.
- The dapp should now be visible on the Parity Wallet Applications page.

## Additional functions

It is highly recommended that dapp developers use the Parity Wallet registry for ease of name lookups, especially when having multiple contracts on multiple chains. [The name registry](Parity-name-registry) makes the association of names to addresses straight forward and transparent, regardless of the chain.
