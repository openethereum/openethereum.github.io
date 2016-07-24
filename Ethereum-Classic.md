# Ethereum Classic

*Ethereum Classic* is a similar chain to the mainnet Ethereum chain, except that the DAO hard fork does not happen. Prior to the hard fork, this was called "dogmatic" mode. Since there now appears to be a not insignificant userbase, Parity continues to support this mode of operation chain through the CLI argument `--chain=classic`.

### Parity Classic

While Parity has no intrinsic support for building a binary which defaults to the classic consensus algorithm, you can hack it together yourself by altering the normal `parity` binary.

To do this, simply open the `parity` binary in your favourite editor (`vi`/`vim` works ok) and replace the one occurance of the string:

`testnet [default: homestead].`

with the string:

`testnet [default: classic].  `

NOTE: There are two invisible spaces after the `.`: don't forget them!
