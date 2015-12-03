### Architecture

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
