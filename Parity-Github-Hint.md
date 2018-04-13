---
title: Parity Github Hint
---

The Parity team has a simple content hash to github repo (and/or url) [mapping contract](https://github.com/paritytech/contracts/blob/master/GithubHint.sol) available. Parity has the ability to, given the content hash, do a reverse lookup against the contract and to retrieve the associated data.

This registering with the dapp and/or contract allows any URL be be identified by a hash of its content. For example, a dapp developer may want to associate and image with his dapp or a token developer may want to associate an image with his token that is to be displayed on the Parity UI. In addition, the registry is useful for developers to register versions of their dapps, the content of their manifests, etc.


## Registering images

Images are an important part of both the Token and dapp ecosystem. It allows for unique visual identification of the token/dapp. To support the registration of images, the Github Hint dapp has a url -> contentHash mode. Steps to registration -

1. Enter the Github Hint dapp
2. Ensure you are on the first tab, i.e. "File Link"
3. Enter the unique URL to the content. In the case of images and/or text residing on Github, it is advised to point to a specific commit, i.e. [https://github.com/paritytech/dapp-assets/blob/master/tokens/gavcoin-64x64.png](https://github.com/paritytech/dapp-assets/blob/master/tokens/gavcoin-64x64.png) is probably not a good idea (since updates to the image will create a content hash mismatch) while [https://github.com/paritytech/dapp-assets/blob/9e135f76fe9ba61e2d8ccbd72ed144c26c450780/tokens/gavcoin-64x64.png](https://github.com/paritytech/dapp-assets/blob/9e135f76fe9ba61e2d8ccbd72ed144c26c450780/tokens/gavcoin-64x64.png) is preferable
4. In the case of Github URLs, they will be automatically changed to the `raw.githubusercontent.com` version
5. Take note of the hash once the lookup is done (this provided hash is required as metadata in contracts that allow for content storage, e.g. TokenReg or DappReg)
6. Select the account to use as owner (owners can update the URL for a specific hash)
7. Register.

For file registration, any type of URL is supported, it doesn't have to reside on Github.

## Registering manifests

Dapp developers can register their `manifest.json` files with the same steps as they would do for images.


## Registering dapp content bundles

To load dapps from the network, the GHH dapp can be used to register a specific commit. Once the dapp is requested, Parity will download a .zip of the repo in the background and serve the content. Unlike file registration (images and/or manifests), the underlying contract currently only supports github with a specific commit. Steps to registration -

1. Enter the Github Hint dapp
2. Ensure you are on the second tab, i.e. "Content Bundle"
3. Enter the unique owner/repo (example `gavofyork/gavcoin`) as well as the specific commit (example `e960db6bada2aafca9d07a0da952d2e66f902dff`)
4. Take note of the hash once the lookup is done (this provided hash is required as metadata in contracts that allow for content storage, e.g. DappReg)
5. Select the account to use as owner (owners can update the URL for a specific hash)
6. Register
