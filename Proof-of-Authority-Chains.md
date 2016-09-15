We have an initial Proof-of-Authority consensus method, along with sealing infrastructure. Proof-of-Authority is a replacement for Proof-of-Work, which can be used for private chain setups.

It does not depend on nodes solving arbitrarily difficult mathematical problems, but instead uses
a hard-configured set of "authorities" - nodes that are explicitly allowed to create new blocks and secure the blockchain. As a result, the block creation process is much quicker than with public blockchains like Bitcoin or Ethereum - the system allows for sub-second block times. Apart from that, the PoA blockchain is irreversible and, therefore, offers near-instant transaction finality.

### Notes

This method is not BFT; however, it should work well enough for development and demo purposes. Purity will include a fully compatible BFT consensus mechanism later this year.

NOTE: Currently, this will seal a block whenever a new transaction comes through. To be made better we need a timer which will wait for one second after the last block before sealing a new one - better still would be to cooperatively interleave blocks with other sealing nodes. [These](https://github.com/ethcore/parity/issues/1034) [features](https://github.com/ethcore/parity/issues/1035) are planned for development soon.

### Configuring

Such chains can be configured by providing a `--chain` JSON file with such an engine:

```
	"engine": {
		"BasicAuthority": {
			"params": {
				"gasLimitBoundDivisor": "0x0400",
				"durationLimit": "0x0d",
				"authorities" : ["0x..."]
			}
		}
	}
```

If you're expecting to seal blocks, make sure you have `--author` set to one of the `authorities` and that said key is `--unlock`ed (with `--password` file provided). You should ensure anyone else you want sealing on the network is similarly configured.
