So Ethereum is released. How come the dream of an open, transparent, jurisdiction-neutral techno-legal system hasn't arrived?!

Well, it turns out that "Ethereum" (Frontier, eth, geth, whatever) is actually only a relatively small piece of the puzzle. The "kernel" of an operating system, if you like. While planned upgrades to this kernel like proof-of-stake (nudging throughput, providing finality and increasing the network's green credentials) and light-client support (reducing bandwidth, memory, hard-disk and computation requirements on some nodes) are helpful, there is only so much such one can do to propel the platform forward at this level.

In fact, like Linux-based operating systems, the vast majority of the code lies not in the kernel, but rather in "userland" software, the executables (that typically provide services to the user) and the libraries (provide services to other software). The parallel here to the Ethereum ecosystem are the contracts that run within Ethereum, providing services to the user (by virtue of being included in a Dapp or web-based proxy) or to other contracts.

Like the GNU portion of GNU/Linux provides important pieces of software "infrastructure" for a functional operating system such as a C library and shell tools, Ethereum, at present needs this infrastructure before it can be properly exploited. This article is about mapping out and exploring the interrelationships between those pieces.

While I relate much of this article to Ethereum (since that's the system I'm most familiar with), the points are quite general and can probably be applied to many of the systems around now.

## The Three Pillars

There are three key pillars of a crypto-law ecosystem such as that of Ethereum:

- **Identity** (1) 
- **Assets** (n)
- **Information** (or meta-data - inf)

### Identity

Roughly speaking these are contracts that encode the notion of identifying a *unique individual* actor. They are things with "self". Typically, this will be a person or organisation in the real world ("legal entity") - new technology always tries to imitate old as it finds its feet. However, it will eventually take on the meaning of quite abstract "crypto-legal" entities, of which a multi-signature account would be a primitive example. Eventually, IoT promises, many "inanimate" objects (e.g. door handles and toasters) will gain their own identity, able to make autonomous economic actions and be intrinsically different to other instances of their class.

Ethereum already comes with a low-level notion of identity built in - it's the 160-bit (currently hex-encoded) hash. However, being so low-level it is essentially meaningless. Contracts that manage identity will range from heavily decentralised and transient (e.g. swarms of individuals doing regular synchronised meetings across the world to determine uniqueness), heavily centralised and permanent (e.g. government-issued "cold signing devices") to hybrid meta-identity models (e.g. piggy-backing on pre-existing Facebook/Twitter/PGP identities).

The thing these all have in common is that they provide a service to ascertain to who or what corporeal entity, if anything, a particular address represents.

### Assets

Asset encoding within a crypto-legal system is to attempt to model ownership over a particular amount of a class of "stuff". Assets that initial contracts will model will typically be those we are already used to; real-world item classes (cars, rooms), commodities, stocks and currencies. One important difference between assets and identity is that assets tend to be strictly fungible or, at least, generally interchangeable. In terms of platonic philosophy, assets are prototypes or classes of things or stuff, rather than the individual instances themselves.

Aside from "real" asset tracking, infrastructure will be built to track purely virtual assets. Indeed, Ethereum already has a low-level virtual asset built-in; the Ether "token". However, it is generally too low-level to be for much use for most crypto-law applications.

Asset contracts will be implemented in a multitude of manners, not least as a centralised commodity repository (see e.g. Digix Global) and as a purely algorithmic currency (Gavcoin?). Supply-chain platforms implemented within Ethereum would include components that ascribed "asset-class" meaning within contracts.

### Information

Information-class contracts are those which ascribe information to other contracts or entities within the ecosystem or otherwise supply "global" information, perhaps about externalities. In the first group, we might ascribe particular pieces of information to an identity (is it a real person? what is their name?) or an asset (it is physical? how much does it weigh?). In the second group, we typically think about oracles; contracts which describe external information inside Ethereum - e.g. the current market price of Ether in terms of Euros. However, in principle, it could be many things.


### Building on the Pillars