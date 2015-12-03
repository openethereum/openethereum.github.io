Architecture

Processes
- Ethcore
  - BlockQueue
  - BlockChain [.BlockImporter, .MessageDispatcher]
  - IPC (just routes calls to BlockChain and BlockQueue)

- EthcoreSealer
  - TransactionQueue
  - Sealer (!-> Ethcore)

- Networking (devp2p)
  - EthNetworking (?-> Ethcore, ?-> EthcoreSealer)
  - Network I/O
  - (Other protocol threads)