---
title: External Address Format (SS58)
---
SS58 is a simple address format designed for Substrate based chains. There's no problem with using other address formats for a chain, but this serves as a robust default. It is heavily based on Bitcoin's Base-58-check format with a few alterations.

The basic idea is a base-58 encoded value which can identify a specific account on the Substrate chain. Different chains have different means of identifying accounts. SS58 is designed to be extensible for this reason.

## Basic Format

The basic format conforms to:

```
base58encode ( concat ( <address-type>, <address>, <checksum> ) )
```

This is, the concatenated byte series of address type, address and checksum then passed into a base-58 encoder. The `base58encode` function is exactly as [defined](https://en.wikipedia.org/wiki/Base58) in Bitcoin and IPFS, using the same alphabet as both.

## Address Type

The `<address-type>` is one or more bytes that describe the precise format of the following bytes.

Currently, there exist four valid values:

- 00000000b (0) Polkadot Live (SS58 checksum preimage)
- 00000001b (1) Polkadot Live (AccountId checksum preimage)
- 00101010b (42) Generic Substrate (testnets &c.) (SS58 checksum preimage)
- 00101011b (43) Generic Substrate (testnets &c.) (AccountId checksum preimage)

## Address Formats for Polkadot

There are 16 different address formats, identified by the length (in bytes) of the total payload (i.e. including the checksum).

- 3 bytes: 1 byte account index, 1 byte checksum
- 4 bytes: 2 byte account index, 1 byte checksum
- 5 bytes: 2 byte account index, 2 byte checksum
- 6 bytes: 4 byte account index, 1 byte checksum
- 7 bytes: 4 byte account index, 2 byte checksum
- 8 bytes: 4 byte account index, 3 byte checksum
- 9 bytes: 4 byte account index, 4 byte checksum
- 10 bytes: 8 byte account index, 1 byte checksum
- 11 bytes: 8 byte account index, 2 byte checksum
- 12 bytes: 8 byte account index, 3 byte checksum
- 13 bytes: 8 byte account index, 4 byte checksum
- 14 bytes: 8 byte account index, 5 byte checksum
- 15 bytes: 8 byte account index, 6 byte checksum
- 16 bytes: 8 byte account index, 7 byte checksum
- 17 bytes: 8 byte account index, 8 byte checksum
- 34 bytes: 32 byte account id, 2 byte checksum

## Checksum types

Several potential checksum strategies exist within Polkadot, giving different length and longevity guarantees. There are two types of checksum preimage (known as SS58 and AccountID) and many different checksum lengths (1 to 8 bytes).

In all cases for Polkadot, the [Blake2-256](https://en.wikipedia.org/wiki/BLAKE_(hash_function)) hash function is used. The variants simply select the preimage used as the input to the hash function and the number of bytes taken from its output.

The bytes used are always the left most bytes. The input to be used is one of:

- **SS58**: The non-checksum portion of the SS58 byte series used as input to the base-58 function, i.e. `concat( <address-type>, <address> )`.
- **AccountID**: The actual 32-byte account identifier. For externally controlled accounts, this is the public key.

The advantage of using more checksum bytes is simply that more bytes provide a greater degree of protection against input errors and index alteration at the cost of widening the textual address by an extra few characters. For the account ID form, this is insignificant and therefore no 1-byte alternative is provided. For the shorter account-index formats, the extra byte represents a far greater portion of the final address and so it is left for further up the stack (though not necessarily the user themself) to determine the best tradeoff for their purposes.

The advantages of using the SS58 preimage format is that the resultant address may be trivially checked for validity, even when offline. However, when combining this with the account-index short-forms, the format doesn't manage the concern that the same index may eventually represent a different underlying address, perhaps one that the original owner doesn't hold. Using the AccountID preimage format means that any change in the underlying account-id must have the same initial portion of hash-output as the original account-id. At lower sizes of checksum, this does little more than prevent accidentally using an old SS58 address. For higher sizes it provides modest, but quantifiable, security against a casual attacker, requiring the attacker to find an address whose Blake2 hash that shares upto 8 bytes with the original.
