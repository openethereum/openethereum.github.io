---
title: FAQ - User Interface, Wallet and DApps
---

## How can I access the Parity Wallet?

Since Parity v1.10, the Parity Wallet is accessible within Parity UI app.
Please follow [these instructions](https://wiki.parity.io/Parity-Wallet)

## Can I use Mist with Parity?

Yes, run `parity --geth` in Geth-compatibility mode. This sets the IPC path to be the same as Geth's and allows Mist to connect to Parity. See also [Using Parity with Mist](Using Parity with Mist).

## Can I send Bitcoin to my Parity wallet?

No, sending Bitcoin directly to an Ethereum address does not work. However, you can use the Shapeshift integration. Open an account on your wallet and click the blue fox icon.

![Parity Shapeshift Integration](https://i.imgur.com/C7cAYeb.png)

## Can I create multi-signature wallets with Parity?

No, Parity only allows watching wallets and interact with them (view and sign transactions, send funds..). It does not allow to create multi-sig wallets. To watch a multi-sig wallet within Parity UI, [follow these instructions](https://wiki.parity.io/Accounts%2C-Wallets%2C-Vaults#wallets).

## How can I compile and deploy contracts with Parity?

Open your Parity UI and navigate to the _'Contracts'_ tab, it allows you to write, compile and directly deploy contracts on the selected chain.

![Parity Solidity Contracts](https://i.imgur.com/2xjUkiI.png)

## What DApps are available for Parity?

Parity comes with a couple of builtin DApps:

- **Web 2.0 browser**: A Web 2.0 hosted pseudo-dapps browser.
- **Method registry**: A registry of method signatures for lookups on transactions.
- **Parity/Web3 console**: A Javascript development console complete with web3 and parity objects.
- **Registry**: A global registry of addresses on the network.
- **Token Deployment**: Deploy new basic tokens that you are able to send around.
- **Token Registry**: A registry of transactable tokens on the network.
- **TX-Queue Viewer**: Have a peak on the internals of transaction queue of your node.

Additional DApps can be added, see [Writing Dapps for Parity](Writing Dapps for Parity).

## How can I write a DApp for Parity?

Head over to the excellent [DApp Tutorial](Tutorial-Part-1.md) documentation. It walks you through making a simple Ethereum-powered, distributed app. By the end of it, you'll be able to head into Parity, select your Dapp and see it in action.

## Does Parity support the Swarm and Whisper sub-protocols?

Currently, Parity does neither support Swarm nor Whisper sub-protocols. However, [Whisper Support](https://github.com/paritytech/parity/issues/4685) is on the road-map.

## How to connect Parity UI to a node on my network?

You can specify the Websocket IP and port that Parity UI should use to connect to a node. To do so, launch `parity-ui` with the following flags:
```bash
    --ws-interface=[IP]
        Specify the hostname portion of the WebSockets server 
        Parity UI will connect to. IP should be an 
        interface's IP address. (default: 127.0.0.1)

    --ws-port=[PORT]
        Specify the port portion of the WebSockets 
        server Parity UI will connect to. (default: 8546)
```
