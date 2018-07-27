---
title: Overview of Dapp development
---

Dapps are an exciting way to showcase a contract or a series of contracts with a simple interface that allows users to interact with them in a secure way. This guide will walk you through the steps to develop a Dapp for your contract and integrate it into Parity UI.

Parity UI supports two types of Dapps: local Dapps (they sit on your computer and are ideal for the development process or for private Dapps) and network Dapps (Dapps available to the whole community).

Dapps are essentially web apps. A Dapp is a folder with at least two required files:
* `index.html`, your Dapp's main page, its "entry point": this page will be displayed when someone navigates to your Dapp
* `manifest.json`, which provides various information about your Dapp

## Local Dapps

Local Dapps are Dapps located on your machine. Parity UI loads them automatically and displays them only to you.

You will typically want to use a local Dapp during the Dapp development process. Once you're satisfied with your Dapp, you can, if you so wish, deploy it to the network (make it a network Dapp) so that anyone can access it.

Local Dapps are a good way to become familiar with the Ethereum and Parity UI ecosystem.

### Building a local Dapp

Parity UI will load and display any Dapp located in the Parity UI `dapps` folder:
* `~/.config/parity-ui/dapps/` on Linux
* `~/Library/Application Support/parity-ui/dapps/` on Mac OS
* `C:\Users\<you>\AppData\Local\parity-ui\dapps\` on Windows

To make a new local Dapp, start by creating a folder for it, for example `~/.config/parity-ui/dapps/my-first-dapp/`.

Next, you need to set up some files in your Dapp folder, at a minimum `index.html` and `manifest.json`.

Those files can be created either manually or using [chevdor's Dapp generator](https://github.com/chevdor/generator-ethdapp), the latter supporting both web3 and parity.js apps.

We will now walk you through the creation of those files.

#### manifest.json

The `manifest.json` file provides information about your Dapp. A sample is given below:

```json
    {
        "id": "my-first-dapp",
        "name": "Cool Dapp",
        "iconUrl": "images/title.png",
        "description": "This is just a toy dapp with almost no functionality",
        "version": "0.0.1",
        "author": "Joe Bloggs"
    }
```

Here are the fields recognized by Parity UI:
* `id` (required) - A unique identifier for your Dapp
* `name` (required) - The name of your Dapp as it will be displayed on Parity UI
* `iconUrl` - Link to the Dapp icon, relative to the folder of the Dapp: for example `icon.png`
* `description` - Additional information about your Dapp that will be displayed in various Dapp browsers
* `version` - Version of your Dapp
* `author` - Name of the person or organization that made the Dapp
* `localUrl` - If set, Parity UI will display the given URL, for example `http://localhost:3001`, instead of `index.html` when the user navigates to the Dapp. This setting is useful when developing with live or hot reloading.

#### index.html

`index.html` is the entry point of your Dapp. This page will be displayed when someone goes to your Dapp. An example is given below:

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>My Dapp</title>
    <link href="app.css" rel="stylesheet" type="text/css">
  </head>
  <body>
    <div id="container"></div>
    <script src="app.js"></script>
  </body>
</html>
```

In the above html, `app.css` will be your Dapp-specific CSS styles and `app.js` will contain the specific Dapp logic.

Note that Parity UI will automatically inject a global `ethereum` JavaScript object into your Dapp, as well as the legacy `parity` and `web3` objects. These variables will let you communicate with your node and thereby interact with the network.

#### app.css

A very simple css file could contain the following:

```css
#container {
  margin: 1em;
  font-size: 1.5em;
  background: #eee;
  border-radius: 5px;
  padding: 1.5em;
}
```

#### app.js

For a very simple application displaying the blockNumber, we would write the following:

```js
window.parity.api.subscribe('eth_blockNumber', function (error, blockNumber) {
  if (error) {
    console.log('error', error);
    return;
  }

  document.getElementById('container').innerHTML = blockNumber.toFormat(0);
});
```

## Network Dapps

Once your local Dapp operates as intended, you might want to deploy your Dapp to the network. To do so, Parity UI includes a Dapp Registry application to register the manifest, icon and content. The instructions to deploy your Dapp to the network are [detailed here](Register-your-DAPP-for-discovery). You might also want to get familiar with the smart contracts and Dapps involved in the process: [Parity Name Registry](Parity-name-registry), [Parity GitHub Hint](Parity-Github-Hint) and [Parity Dapp Registry](Parity-dapp-registry).

- It is recommended to do the deployment on the test network first.
- [Register the Dapp](Parity-dapp-registry) to receive a new unique network id.
- Generate the correct content hashes [via the GitHubHint Dapp](Parity-Github-Hint) (GHH).
  - For manifests and files, you should use the _File Link_ option
  - For the actual Dapp content bundle, you should use the _Content Link_ option
- Edit the Dapp [in the Dapp Registry](Parity-dapp-registry) and supply the image, manifest and content hashes as generated in the previous step.
- The Dapp should now be visible in Parity UI.

## Additional notes

It is highly recommended that Dapp developers use the Parity UI registry for easy name lookups, especially when having multiple contracts on multiple chains. [The name registry](Parity-name-registry) associates names to addresses in a straightforward and transparent manner, regardless of the chain.
