# Ethereum Classic

*Ethereum Classic* is a similar chain to the mainnet Ethereum chain, except that the DAO hard fork does not happen. Parity fully supports the Classic chain through using the CLI argument `--chain=classic`.

### Parity Classic

If you wish to make your `parity` binary into a true "Parity Classic" binary (and thus default to starting in Classic mode), simply open the `parity` binary in your favourite editor (`vi`/`vim` works ok) and replace all occurances of the string:

`                           testnet [default: homestead].`

with the string:

`                           testnet [default: classic].&nbsp;&nbsp;`

NOTE: There are two invisible spaces after the `.`: don't forget them!

You can rename the resulting `parity` binary to `parity-classic` and hey presto you now have a 100% Classic-compatible Parity client.