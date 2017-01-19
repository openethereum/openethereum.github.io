# Parity Wallet

Parity includes a bundled wallet application.
This is designed to integrate seamlessly with all standard tokens as well as Ether itself.

To access the UI, simply start your Parity node and navigate to http://127.0.0.1:8180. There, you will see
a page where you can manage accounts, create transactions, watch contracts, and interact with DApps.

## Secure Transaction Signer
The Secure Transaction Signer is a core piece of technology which ensures that no transaction can be sent from your accounts without your explicit approval. This means that you can interact with DApps without fear that they might attempt to drain your accounts. 

When a transaction request is made, you'll have to use the Signer UI to authenticate the request before the transaction can be signed relayed to the network. This is made to be as seamless as possible, and you can rest easy knowing your Ether is protected.

Here's a picture of it in action:
[[images/sending_signing.png]]

One more thing: see the purple-ish background? Every person's UI has its own unique, procedurally-generated background. This means that it's impossible for another site to pretend to be your signer. You get to choose your background from a selection of unique candidates whenever you'd like, so you can always find one you enjoy.