So Ethereum is released. How come the dream of an open, transparent, jurisdiction-neutral techno-legal system hasn't arrived?!

Well, it turns out that "Ethereum" (Frontier, eth, geth, whatever) is actually only a relatively small piece of the puzzle. The "kernel" of an operating system, if you like. While planned upgrades to this kernel like proof-of-stake (nudging throughput, providing finality and increasing the network's green credentials) and light-client support (reducing bandwidth, memory, hard-disk and computation requirements on some nodes) are helpful, there is only so much such one can do to propel the platform forward at this level.

In fact, like Linux-based operating systems, the vast majority of the code lies not in the kernel, but rather in "userland" software, the executables (that typically provide services to the user) and the libraries (provide services to other software). The parallel here to the Ethereum ecosystem are the contracts that run within Ethereum, providing services to the user (by virtue of being included in a Dapp or web-based proxy) or to other contracts.

Like the GNU portion of GNU/Linux provides important pieces of software "infrastructure" for a functional operating system such as a C library and shell tools, Ethereum, at present needs this infrastructure before it can be properly exploited. This article is about mapping out and exploring the interrelationships between those pieces.

While I relate much of this article to Ethereum (since that's the system I'm most familiar with), the points are quite general and can probably be applied to many of the systems around now.

## The Three Pillars

There are three key pillars of a crypto-law ecosystem such as that of Ethereum:

- **Identity** (1) 
- **Assets** (n)
- **Data** (inf)

### Identity

Roughly speaking these are contracts that encode the notion of identifying a *unique individual* actor. They are things with "self". Typically, this will be a person or organisation in the real world ("legal entity") - new technology always tries to imitate old as it finds its feet. However, it will eventually take on the meaning of quite abstract "crypto-legal" entities, of which a multi-signature account would be a primitive example. Eventually, IoT promises, many "inanimate" objects (e.g. door handles and toasters) will gain their own identity, able to make autonomous economic actions and be intrinsically different to other instances of their class.

Ethereum already comes with a low-level notion of identity built in - it's the 160-bit (currently hex-encoded) hash. However, being so low-level it is essentially meaningless. Contracts that manage identity will range from heavily decentralised and transient (e.g. swarms of individuals doing regular synchronised meetings across the world to determine uniqueness), heavily centralised and permanent (e.g. government-issued "cold signing devices") to hybrid meta-identity models (e.g. piggy-backing on pre-existing Facebook/Twitter/PGP identities).

The thing these all have in common is that they provide a service to ascertain to who or what corporeal entity, if anything, a particular address represents.

### Assets

Asset encoding within a crypto-legal system is to attempt to model ownership over a particular amount of a class of "stuff". Assets that initial contracts will model will typically be those we are already used to; real-world item classes (SKUs, make & models), commodities, stocks and currencies. One important difference between assets and identity is that assets tend to be strictly fungible or, at least, generally interchangeable. In terms of platonic philosophy, assets are prototypes or classes of things or stuff, rather than the individual instances themselves.

Aside from "real" asset tracking, infrastructure will be built to track purely virtual assets. Indeed, Ethereum already has a low-level virtual asset built-in; the Ether "token". However, it is generally too low-level to be for much use for most crypto-law applications.

Asset contracts will be implemented in a multitude of manners, not least as a centralised commodity repository (see e.g. Digix Global) and as a purely algorithmic currency (Gavcoin?). Supply-chain platforms implemented within Ethereum would include components that ascribed "asset-class" meaning within contracts.

### Data

Data contracts are those which ascribe information to other entities within the ecosystem or otherwise supply "global" information, perhaps about externalities. In the first group, we might ascribe particular pieces of information to an identity (is it a real person? what is their name?) or an asset (it is physical? how much does it weigh?). In the second group, we typically think about oracles; contracts which describe external information inside Ethereum - e.g. the current market price of Ether in terms of Euros. However, in principle, the information could be entirely internal; metadata concerning a currency for example such as its name and code.

Data-infrastructure differs from the other two pillars in that it provides no directly referencable or manipulatable concepts; rather it exists solely to add richness of meaning to other existential entities, be they asset-classes or identities. It will likely be implemented mostly as an on-chain hash with an off-chain reverse-hash-lookup service, though more advanced hybrid system may not need to make the distinction as our relatively unbaked protocols do at present.

Ethereum, perhaps now unsurprisingly, also contains a few built-in pure information services, too. For example, the EVM operation codes allowing a contract to retrieve the blocknumber and timestamp provide global information points. While a timestamp is a relatively high-level piece of information, it sits essentially alone against the other information-giving operation codes such as the block number.

## Building on the Pillars

While contracts that provide pure services concerning each one of these pillars will be extremely useful, these should be considered but simply a palette from which the crypto-lawyer may mix an appropriate shade. Combinations of these base layer services will lead to far greater, more meaningful and more relevant services being provides in this ecosystem.

### Data + Assets
- **Asset Classification** The simplest combination of data and assets, this would just be data attached to assets to express attributes, perhaps in a structured fashion.
- **Pegged Currency** Through the combination of the primitives of a data-feed and an asset class, we can create an economic model of a currency which tracks a real-world value feed, such as the US Dollar. There are several possible ways of implementing this including through CFD-shares and so called "stable/volatile-coin" dualisms.

### Assets + Identity
- **Certification** Through attaching a particular non-ownership relationship between an identity and an asset, we can synthesise the notion of certification. The essential meaning is that while identity X (e.g. "Organic") does not own asset-class Y (e.g. coffee beans in my shipment) it associates its intrinsic notion with it, labelling my shipment of coffee beans as having been grown organically.
- **Asset Tracking** We may apply the relationship inversely: taking an asset class and creating an identity for it, changing the situation from "A pint of beer" to "*this* pint of beer" in a process we might call *instancing*. This can happen when I purchase a particular item or when an item's configuration alters from a pristine state. A smartphone, for example, prior to purchase could easily be consider a fungible asset class each item being not intrinsically different to any other. Following purchase it becomes intrinsically mine along with the data, phone plan and wear that identify it.
- **Judged-Escrow** Escrow services, combining an appeal to an identifiable authority with the ownership (or transfer thereof) provide an important foundation for anonymous trade. Notably, basic escrow services without any authority being involved can exist, though are more useful when combined with badges or a reputation system (next).

### Identity + Data
- **Reputation** For something so commonly used, reputation is a difficult notion to pin down. At its root, reputation is simply an attribute of an identity, a means of linearly measuring the entity's virtuosity and typically related to how well other "identities" perceive it. Reputation systems within a crypto-legal system help make up for the lack of social-contact inherent with purely machine-to-machine systems and form the precursors and prototype for Web-of-Trust and more formal credit-rating and AML/KYC systems.
- **Badges** Badges are essentially a non-transferable, non-linear attribute attached to an identity. Depending on the level of badge-prototyping, they could be argued to be either *Identity + Information* or *Identity + Asset*. Badges of non-structured data may include, e.g. character-testimony. For structured-data they may include notes of events attended (OMG he went to see the first ever episode of Red Dwarf being filmed) or intrinsic roles & rights (I believe in the celestial teapot, I am a human).
- **Trusted Oracles** Reversing the relationship, we can allow an identity to vouch for data. One means of getting reliable information concerning external phenomena is to decide to take a particular identity as ones authority and accept data coming from it. While it is far from the ideal, it is relatively cheap and easy to implement so will likely become the de facto standard.

### The Rest

We can recombine many of these service classes into higher-order crypto-law concepts and services on which we may truly see the beginnings of analogies to modern legal tools and services.

- **Credit Rating**, **KYC** (Reputation) Derivatives from the reputation primitive, credit rating systems provide richer, more meaningful semantics over particular aspects of identity which can be useful primitives for financial systems.
- **Virtual Retail** (Pegged Currency + Assets) Through combining asset classes and pegged currencies, we can imagine an online shop. Asset classes can be swapped for a pegged currency allowing for purely on-chain assets to be bought and sold trustlessly and off-chain assets to be bought from a trustworthy retailer.
- **"Real" Marketplace** (Reputation + Escrow + Virtual Retail) Through the use of a reputation system and escrow, we can imagine a largely trustless decentralised retail system for trading on- and off-chain assets.
- **Truth Engine** (Data + Reputation) Through having reputation-weighted voting over the validity of particular statements (perhaps backed with an adversarial evidence-submission system). A prediction market provides another, more complex and economically-robust, mechanism to implement such a thing. The Truth Engine is a critical component of many high-level crypto-legal mechanisms such as a crypto-Court.
- **Court** (Truth Engine + Escrow) A theoretical minimal court includes a truth engine to determine the correct course of action together with a "bailiff"; the bailiff component may ultimately be implemented through a ubiquitous system of bonded identities. At present, the most obvious bailiff comes from a per-transaction Escrow service, which holds the assets and has the power to transfer them to any other party.
- Trade Finance (Credit Rating + Pegged Currency)
- "Supply-chain-on-blockchain" (Certification + Asset-tracking)