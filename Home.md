# Parity

Parity is an Ethereum client, written from the ground-up for correctness-verifiability, modularisation, low-footprint and high-performance. To this end it utilises the Rust language, a hybrid imperative/OO/functional language with an emphasis on efficiency. It is professionally developed by Ethcore; we aim to have all important logic 100% unit-tested, all public APIs 100% documented, all code reviewed my multiple peers and follow a pipelined 6-ish-week release cycle similar to the Rust compiler.

If you want to get a node up and running with Parity, see the [[Quick-start]] guide. If you're interested in mining with Parity, see the [[Mining]] guide. If you'd like to use the Web3 Javascript interface with Parity, see the [[Developers]] guide. If you're interested in hacking on the Parity code base see the [[Coding-guide]].

# Our Tenets

#### Minimise Moving Parts

While Rust is multi-paradigm, we aim to write intra-function logic in as functional a manner as possible. Mutability is avoided except where necessary for the algorithm or efficiency.

#### Readable Code == Verifiable Code

Which is very important when you're coding to a formal specification.

#### Modularise and Isolate

Isolatable code means easier code in which to track problems and harder code to make wrong with additional features.

#### Minimise Footprint

#### Maximise Performance

#### Design for Use

## Our Priorities

#### Compatibility

Where standards (published or de facto) exist, we strive to honour them. e.g. in the case of CLI options, wherever we share a feature with geth, Parity supports `geth`'s CLI option for that feature. e.g. in order to set the destination address (i.e. the account which will be credited with any mining rewards), you can use either of `--etherbase` (in geth dialect) or `--author` (the Parity variant). Parity doesn't care.

