This tutorial will walk through setting up two Parity nodes locally and sending transactions between them. If you want to learn more about different parameters specified in this tutorial please refer to the [[Chain specification]] page.

## 1. Get Parity

## 2. Choose your chain

We will run a chain with Authority Round consensus engine.

`"gasLimitBoundDivisor"` which determines gas limit adjustment has the usual Ethereum value  
`"stepDuration"` is set to at least 5 second block times  
`"authorities"` is empty right now since we are yet to create our authority accounts  

`"params"` are left to be fairly standard#

`"genesis"` has some standard values for the Authority Round consensus

`"accounts"` contains the standard Ethereum builtin contracts, these should be included to use the Solidity contract writing language

```
{
    "name": "DemoPoA",
    "engine": {
        "authorityRound": {
            "params": {
                "gasLimitBoundDivisor": "0x400",
                "stepDuration": "5",
                "authorities" : []
            }
        }
    },
    "params": {
        "maximumExtraDataSize": "0x20",
        "minGasLimit": "0x1388",
        "networkID" : "0x2323"
    },
    "genesis": {
        "seal": {
            "authorityRound": {
                "step": "0x0",
                "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
            }
        },
        "difficulty": "0x20000",
        "gasLimit": "0x5B8D80"
    },
    "accounts": {
        "0x0000000000000000000000000000000000000001": { "balance": "1", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
        "0x0000000000000000000000000000000000000002": { "balance": "1", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
        "0x0000000000000000000000000000000000000003": { "balance": "1", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
        "0x0000000000000000000000000000000000000004": { "balance": "1", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } }
    }
}
```

## 3. Create accounts

Now that a bare bones chain specification is complete new accounts can be created. Parity stores accounts for each chain with a different genesis hash in a separate folder, so in order to create the accounts in the correct one we will need to run with the `--chain` option.

## 4. Complete the chain specification

## 5. Run the nodes

## 6. Connect them together

## 7. Send transactions

## 8. Further development