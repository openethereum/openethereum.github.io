Starting with 1.8.0 Parity supports on-chain transaction permissioning for private chains. A smart contract can be deployed that regulates if two nodes con connect to each other given their enode IDs.
The contract must be deployed on the chain and it's address added to the chain spec file under `"params"/"transactionPermissionContract"`.

The contract must support the following ABI
```json
[
  {
    "constant": false,
    "inputs": [
      {
        "name": "sender",
        "type": "address"
      }
    ],
    "name": "allowedTxTypes",
    "outputs": [
      {
        "name": "",
        "type": "uint32"
      }
    ],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```
Here's a sample contract that implements that ABI:

```solidity
pragma solidity ^0.4.11;

contract PeerManager {
    mapping(bytes32 => bytes32) peers;
    
    function PeerManager() {
        peers[0x11] = 0x12;
        peers[0x21] = 0x22;
        peers[0x31] = 0x32;
        peers[0x41] = 0x42;
    }
    
    function connectionAllowed(bytes32 sl, bytes32 sh, bytes32 pl, bytes32 ph) constant returns (bool res) {
        if (sl == 0x01 && sh == 0x02) {
	    return true;
	}
	return pl != 0 && ph != 0 && peers[pl] == ph;
    }
}
```
Here `sl` is low 32 bytes of peer 1 enode Id, `sh` is hi 32 bytes of peer 1 enode Id, `pl` is low 32 bytes of peer 2 enode Id, `ph` is hi 32 bytes of peer 2 enode Id.