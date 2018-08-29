---
title: Part 4 - Send private transactions
---

Now that our private contract is deployed, we can interact with it. In this section, we will see how to call the contract, change its state and verify the state change.

## 1. See the value of x

In our private smart contract, the variable `x` is set as public, but there is no explicit function to view its state. The reason is that in solidity, a getter function is automatically created for any public variable state. Any public variable such as `x` is viewable, in our case, using the function `x()`. See [here](http://solidity.readthedocs.io/en/v0.4.24/contracts.html#getter-functions) for more info.

To call a function of a smart contract with an RPC call, one needs to pass in the `data` field the signature of the function. For a public smart contract, one would use `eth_call`. Now since we want to access the state of a variable for a private contract, we need to use `private_call`.

To find the signature of a function, we need to take the first 4 bytes of the keccak of the function, without any variable name. We can do it with the following:
```bash
parity tools hash <(echo -n "x()")
0c55699c4769c0b08a59db8a53119b799ea10ec55a4da92af76b0404363e0980
```

In this case, `0c55699c` is the signature of `x()`.

We can now call `private_call`. Note that you will have to set the nonce manually. Simply increment it compared to the previous call. If the nonce is wrong, an error will appear mentioning which nonce value was expected.

```bash
curl --data '{"method":"private_call","params":["latest",{"from":"0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046","to":"0x4466399893d182dcbe43c2a5a79db93b52ed26ae","data":"0x0c55699c", "nonce":"0x18"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```
- `0xe5a4b6f..29b046` is Alice's address.
- `0x4466399..ed26ae` is the public contract address received in [Part 3, step 3.1](#31-encrypt-the-deployment-transaction-for-the-validators).
- `0x0c55699c` is the signature of `x()` calculated previously
- `0x18` is the correct nonce in this particular case as the last nonce used in [Part 3, step 3.1](#31-encrypt-the-deployment-transaction-for-the-validators) was `0x17`. You will likely have another value.

The result is the current value of `x`. As it wasn't initialized, it defaults to 1.
```json
{"jsonrpc":"2.0","result":"0x0000000000000000000000000000000000000000000000000000000000000001","id":1}
```

## 2. Change the value of x

We will now change the value of X by calling `setX` function. If we were interacting with a regular public smart contract, we would compose, sign and finally send a transaction using `eth_sendRawTransaction`. Now since we interact with a private contract, we will compose, sign and finally send the transaction using `private_sendTransaction`.

### 2.1 Compose the transaction

As we did earlier, we will need to generate the signature of the function we which to call. In this case `setX(bytes32)`:
```bash
parity tools hash <(echo -n "setX(bytes32)")
bc64b76d46f38211b46cd1a107337ffa1958d706b339b2a10aee4ea3d594b474
```

As explained before, the first 4 bytes:`bc64b76d` represents the signature. To call `setX(42)`, we need to pass the variable padded with 0 to 32 bytes. In hex `42` is `0x2a`. We now have all the needed elements to compose the transaction.

```bash
curl --data '{"method":"parity_composeTransaction","params":[{"from":"0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046","to":"0x4466399893d182dcbe43c2a5a79db93b52ed26ae","data":"0xbc64b76d000000000000000000000000000000000000000000000000000000000000002a"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```
- `0xe5a4b6f..29b046` is Alice's address.
- `0x4466399..ed26ae` is the public contract address received in [Part 3, step 3.1](#31-encrypt-the-deployment-transaction-for-the-validators).
- `0xbc64b76..00002a` is the signature of `setX()` calculated previously with `42` as argument.

The answer is the transaction including all the needed arguments such as the amount of gas that should be used, the proper nonce etc..
```json
{
    "jsonrpc": "2.0",
    "result": {
        "condition": null,
        "data": "0xbc64b76d000000000000000000000000000000000000000000000000000000000000002a",
        "from": "0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046",
        "gas": "0xe57e0",
        "gasPrice": "0x0",
        "nonce": "0x18",
        "to": "0x4466399893d182dcbe43c2a5a79db93b52ed26ae",
        "value": "0x0"
    },
    "id": 1
}
```

### 2.2 Sign and send the transaction

We can now use the arguments received in the previous step to sign the transaction by calling `personal_signTransaction`. Note that up to now, these steps are not different than the one you would performed for any type of contract call.
```bash
curl --data '{"method":"personal_signTransaction","params":[{"condition":null,"data":"0xbc64b76d2a00000000000000000000000000000000000000000000000000000000000000","from":"0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046","gas":"0xe57e0","gasPrice":"0x0","nonce":"0x18","to":"0x4466399893d182dcbe43c2a5a79db93b52ed26ae","value":"0x0"},"alicepwd"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```

The result is the transaction signed with Alice's keys.
```json
{
    "jsonrpc": "2.0",
    "result": {
        "raw": "0xf8841880830e57e0944466399893d182dcbe43c2a5a79db93b52ed26ae80a4bc64b76d2a0000000000000000000000000000000000000000000000000000000000000046a070587f5180901cca38c7b9d5b7e9d408ade342fa3f6cc16fc34c8e1df0bfe7eda040d8c3bbc695a4ab5c8fcbfd7f35b7b9e6352e5fb4a5113f63f1f67ab6d38b32",
        "tx": {
            "blockHash": null,
            "blockNumber": null,
            "chainId": "0x11",
            "condition": null,
            "creates": null,
            "from": "0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046",
            "gas": "0xe57e0",
            "gasPrice": "0x0",
            "hash": "0x13724c166e0eba0e9a3adc32d8887494eba877bdaaca4ac8471014b87ac13e4f",
            "input": "0xbc64b76d2a00000000000000000000000000000000000000000000000000000000000000",
            "nonce": "0x18",
            "publicKey": "0xc20eea90083d48d53356423038056bc896b5f63ace0c4a8861deaffc5130da7747c7a2274165060f8f39c707e15dea47098a402e05c5e326b8d4f07d896edd86",
            "r": "0x70587f5180901cca38c7b9d5b7e9d408ade342fa3f6cc16fc34c8e1df0bfe7ed",
            "raw": "0xf8841880830e57e0944466399893d182dcbe43c2a5a79db93b52ed26ae80a4bc64b76d2a0000000000000000000000000000000000000000000000000000000000000046a070587f5180901cca38c7b9d5b7e9d408ade342fa3f6cc16fc34c8e1df0bfe7eda040d8c3bbc695a4ab5c8fcbfd7f35b7b9e6352e5fb4a5113f63f1f67ab6d38b32",
            "s": "0x40d8c3bbc695a4ab5c8fcbfd7f35b7b9e6352e5fb4a5113f63f1f67ab6d38b32",
            "standardV": "0x1",
            "to": "0x4466399893d182dcbe43c2a5a79db93b52ed26ae",
            "transactionIndex": null,
            "v": "0x46",
            "value": "0x0"
        }
    },
    "id": 1
}
```

We can now use the dedicated function `private_sendTransaction` to broadcast the transaction to the validators for them (in our case, only Bob) to read it and change the state of the contract accordingly.

```bash
curl --data '{"method":"private_sendTransaction","params":["0xf8841880830e57e0944466399893d182dcbe43c2a5a79db93b52ed26ae80a4bc64b76d000000000000000000000000000000000000000000000000000000000000002a46a070587f5180901cca38c7b9d5b7e9d408ade342fa3f6cc16fc34c8e1df0bfe7eda040d8c3bbc695a4ab5c8fcbfd7f35b7b9e6352e5fb4a5113f63f1f67ab6d38b32"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545 
```

- `0xf88418..d38b32` is the `raw` data received at the previous step.

The result is the transaction hash that we will not use.
```json
{
    "jsonrpc": "2.0",
    "result": {
        "contractAddress": 0x4466399893d182dcbe43c2a5a79db93b52ed26ae,
        "status": 0,
        "transactionHash": "0x13724c166e0eba0e9a3adc32d8887494eba877bdaaca4ac8471014b87ac13e4f"
    },
    "id": 1
}
```

If this step was performed successfully, you should see the following line in the console logs of Alice's node. This indicates that the state of the private contract was successfully changed.
```bash
Transaction mined (hash 0x13724c166e0eba0e9a3adc32d8887494eba877bdaaca4ac8471014b87ac13e4f)
```

### 3. Verify the state change

We can now use the same call as in step 1. (with an updated nonce) to read the state of `x`:
```bash
curl --data '{"method":"private_call","params":["latest",{"from":"0xe5a4b6f39b4c3e7203ca8caeecbad58d8f29b046","to":"0x4466399893d182dcbe43c2a5a79db93b52ed26ae","data":"0x0c55699c", "nonce":"0x19"}],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8545
```
- `0xe5a4b6f..29b046` is Alice's address.
- `0x4466399..ed26ae` is the public contract address received in [Part 3, step 3.1](#31-encrypt-the-deployment-transaction-for-the-validators).
- `0x0c55699c` is the signature of `x()` calculated previously
- `0x19` is the correct nonce incremented by 1 compared to the previous call. 

The state has been updated!
```json
{
    "jsonrpc": "2.0",
    "result": "0x000000000000000000000000000000000000000000000000000000000000002a",
    "id": 1
}
```

|[ ← Part 3 - Private contract deployment ](Private-Transactions-Tutorial-3.md)|


