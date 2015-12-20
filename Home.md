# Ethcore Software Strategy

## Release Timeline

- **Parity** _Jan 2016_ Basic `eth`-like implementation.
  - Uses libevmjit, libethash, `ethminer`.
  - Passes all consensus tests.
- **Civility** Fully actor-based `eth`-like implementation.
  - Follows from Parity codebase.
- **Fluidity** Build on Basic on-chain PoA consensus client.
  - Follows from Comity codebase.
- **Purity** EIP101-like implementation.
  - Forked from Comity codebase.
  - Precursor to Serenity.
- **Density** Scalable PoA variant.
  - Basic chain-fibre implementation.

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
