# Ethcore Software Strategy

# PARITY

Our initial product. All Parity releases run support the Ethereum mainnet. General goals are good-optics, riding the Ethereum hype wave, attaining mindshare, and displaying activity and ability. The releases will be staged with one happening roughly every 6 weeks.

Teams will likely pipeline releases (i.e. bigger) releases will be developed in tandem with smaller releases and staggered.

## Pre-release
- Code: parity-0.9
- Date: e.o. Jan 2016 (est.)

### Primary goals
- Demonstrate activity and ability

### Secondary goals
- Provide experience in Rust coding
- Provide bed of code for further development

## Parity
- Code: parity-1.0
- Date: mid Mar 2016 (est.)

### Primary goals
- Provide viable alternative client to `eth`.

### Secondary goals
- Achieve adoption of slock.it

## Civility
- Code: parity-1.1
- Date: e.o. April 2016 (est)

### Primary Goals
- Introduce high-reliability modular architecture

### Secondary Goals
- Provide useful tooling (e.g. `ethkey`)

## Tenuity
- Code: parity-1.2
- Date: mid June 2016 (est)

### Primary Goals
- Introduce full light client support.

## Affinity
- Code: parity-1.3
- Date: Autumn 2016

### Primary Goals
- Introduce privacy/confidentiality support

# PURITY

Our second product line. This will form the basis for our implementation of the Ethereum release *Serenity*, but also for any custom made bank or consortium chain. It will be largely based around [EIP#101](https://github.com/ethereum/EIPs/issues/28).

Density, the later part of the product line would be based around my original [Chain Fibres](https://github.com/ethereum/wiki/wiki/Chain-Fibers-Redux) idea and [Vitalik's pre-2.0 formalisation](https://github.com/ethereum/EIPs/issues/53). 

Development of this will happen independently of the *PARITY* product line.

## Purity
- Code: purity-1.0
- Date: Winter 2016/17

### Primary Goals
- Removal of currency portions and abstraction.

## Density
- Code: tbd
- Date: Mid 2017

### Primary Goals
- Scalability

## Civility Architecture

#### Processes & Threads

- `Ethcore`
  - `BlockQueue` 
  - `BlockChain` [`.BlockImporter`, `.MessageDispatcher`]
  - `IPC` (just routes calls to `BlockChain` and `BlockQueue`)
- `EthcoreSealer`
  - `TransactionQueue`
  - `Sealer` (!-> `Ethcore`)
- `Networking` (devp2p)
  - `EthNetworking` (?-> `Ethcore`, ?-> `EthcoreSealer`)
  - `I/O`
  - (Other protocol threads)

- ?-> Optional outgoing IPC calls
- !-> Required outgoing IPC calls
