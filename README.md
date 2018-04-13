---
title: Parity
permalink: index.html
---

Parity is an Ethereum client, written from the ground-up for correctness-verifiability, modularisation, low-footprint and high-performance. To this end it utilises the Rust language, a hybrid imperative/OO/functional language with an emphasis on efficiency. It is professionally developed by Parity Technologies, a VC-funded UK-based company. We aim to have all important logic 100% unit-tested, all public APIs 100% documented, all code reviewed by multiple peers and follow a pipelined 7-ish-week release cycle similar to the Rust compiler.

Parity comes with an extensive, in-built Ethereum Wallet and Dapp environment. It includes:

- Accounts and address-book.
- Key creation, importing and exporting.
- Web3 Ðapp browser.
- [Hardware](Ledger-Nano-S) and electronic cold-wallet support.
- [Name registry](Parity-name-registry) support. 
- Contract development, deployment and interaction environment.

To use it, follow [these steps](Parity-Wallet).

Some help:
- If you want to get a node up and running with Parity, see the [Setup](Setup) guide, or browse the [Articles Index](Community-Guides).
- Frequently asked questions are answered on the [FAQ](FAQ).
- If you're interested in mining with Parity, see the [Mining](Mining) guide.
- If you'd like to use the JSONRPC interface with Parity, see the [JSONRPC](JSONRPC) guide.
- If you're interested in hacking on the Parity code base see the [Coding guide](Coding-guide).
- For [Private chains](Private-chains) and [Proof of Authority Chains](Proof-of-Authority-Chains), you might find the [Chain specification](Chain-specification) useful.

## Our Tenets

### Minimise Moving Parts

While Rust is multi-paradigm, we aim to write intra-function logic in as functional a manner as possible. Mutability is avoided except where necessary for the algorithm or efficiency.

### Readable Code == Verifiable Code

Which is very important when you're coding to a formal specification.

### Modularise and Isolate

Isolatable code means easier code in which to track problems and harder code to make wrong with additional features.

### Drive Forward

A steady release cycle, once every 6-7 weeks with a master/beta/stable pipeline provides users with certainty and helps us structure feature-inclusion in a disciplined fashion.

## Our Priorities

### Compatibility

Where standards (published or de facto) exist, we strive to honour them. e.g. in the case of CLI options, wherever we share a feature with geth, Parity supports `geth`'s CLI option for that feature. e.g. in order to set the destination address (i.e. the account which will be credited with any mining rewards), you can use either of `--etherbase` (in geth dialect) or `--author` (the Parity variant). Parity doesn't care.

### Minimum Footprint, Maximum Performance

Maximise references, minimise copying and holding copies. Rust makes it safe. When there are dynamic data structures, provide means for keeping them under control.

### Security

We want to protect our processes from each other and the (possibly malicious) outside world. Through leveraging process isolation, sophisticated memory management and OS-level protection primitives we provide the most secure Ethereum implementation in the world.

### Reliability

Through Rust's language-level memory and thread guarantees and a disciplined approach to exception-handling we can state with a high degree of certainty that our code *cannot* crash, hang or bomb-out unexpectedly.

### Test, Document and Review

Our codebase is lovely. So lovely we frequently get compliments from newcomers. And we want to keep it that way. We strive for 100% coverage of documentation, unit tests and code reviews.

