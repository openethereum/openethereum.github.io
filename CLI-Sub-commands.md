---
title: OpenEthereum CLI Sub-commands
---
## Summary
* [Export state](#export-state)
* [Export blocks](#export-blocks)
* [Import](#import)
* [Account new](#account-new)
* [Account list](#account-list)
* [Account import](#account-import)
* [Db kill](#db-kill)
* [Db reset](#db-reset) 
* [Daemon](#daemon)
* [Snapshot](#snapshot)
* [Restore](#restore)
* [Tools hash](#tools-hash)
* [Signer new-token](#signer-new-token)



## Export state

Export state from a given --chain (default: foundation) to a file.
This command requires the chain to be synced with --fat-db on.

### Flags:
- `--no-code`       Don't export account code.
- `--no-storage`    Don't export account storage.

### Options:
- `--at <BLOCK>`           Take a snapshot at the given block, which may be an index, hash, or latest. Note that taking snapshots at non-recent blocks will only work with --pruning archive
- `--format <FORMAT>`      Export in a given format. FORMAT must be either 'hex' or 'binary'. (default: binary)
- `--max-balance <WEI>`    Don't export accounts with a balance greater than specified.
- `--min-balance <WEI>`    Don't export accounts with balance less than specified.

### Example:
`openethereum export state ./export-state-file.bin`

## Export blocks

Export blocks into the given --chain database (default: foundation).
This command requires the chain to be synced with --fat-db on.

### Options:
- `--format <FORMAT>`    Export in a given format. FORMAT must be either 'hex' or 'binary'. (default: binary)
- `--from <BLOCK>`       Export from block BLOCK, which may be an index or hash.
- `--to <BLOCK>`         Export to (including) block BLOCK, which may be an index, hash or latest.

### Example:
`openethereum export blocks --chain goerli ./export-blocks-goerli.bin`

## Import

Import blockchain from a file to the given --chain database (default: foundation).

### Options:
- `--format <FORMAT>`    Import in a given format. FORMAT must be either 'hex' or 'binary'. (default: auto)

### Example: `openethereum import ./export-blocks-file.bin`

## Account new

Create a new account (and its associated key) for the given --chain (default: foundation).

### Example:
```bash
openethereum --chain goerli account new
Loading config file from users.toml
Please note that password is NOT RECOVERABLE.
Type password: 
Repeat password:
0x66a4b6f39b4c3e7203ab9caeecbad58d8f29b046
```

## Account list

List existing accounts of the given --chain (default: foundation).

### Example:
```bash
openethereum --chain goerli account list
0x00780865dcc3c7dbffd943136107e70b8270286b
0x00dfc93112abd2578503b667b95491b101281f2b
```

## Account import

Import accounts from JSON UTC keystore files to the specified --chain (default mainnet).

### Example:
```bash
openethereum --chain goerli account list
0x00780865dcc3c7dbffd943136107e70b8270286b
0x00dfc93112abd2578503b667b95491b101281f2b


$ openethereum account import --chain goerli ./keystore-file.json 
1 account(s) imported

$ openethereum --chain goerli account list
0x003324332f7a9c2fb9536fcc4246a0bb4ea1b63f
0x00780865dcc3c7dbffd943136107e70b8270286b
0x00dfc93112abd2578503b667b95491b101281f2b
```

## Db kill

Clean the database of the given --chain (default: foundation).
### Example:
```
openethereum db kill --chain goerli
Database deleted.
```

## Db reset 

Removes `<NUM>` latest blocks from the db
### Example:
```
openethereum db reset --chain goerli 5
Successfully reset db!
```


## Daemon

Launch OpenEthereum as a daemon.

### Example:
`openethereum daemon ~/openethereum-daemon.pid --chain goerli`

## Snapshot

Make a snapshot of the database of the given --chain (default: foundation).

### Example:
`openethereum snapshot ~/goerli.snapshot --chain goerli`

### Options:
        --at <BLOCK>    Take a snapshot at the given block, which may be an index, hash, or "latest". Note that taking
                        snapshots at non-recent blocks will only work with --pruning archive

## Restore

Restore database of the given --chain (default: foundation) from snapshot.

### Example:
`openethereum restore ~/goerli.snapshot --chain goerli`

## Tools hash

Hash a file using Keccak-256 algorithm.

### Example:
```
openethereum tools hash ./some_file_to_hash.zip
aa7c3630b1c735777964b7a60ab1c2db9059d369ecb12f155778ca0f408b849c
```

## Signer new-token

Generate new token for the given --chain (default: foundation).

### Example:
```bash
openethereum signer new-token --chain goerli

xAtk-66Q1-lPu5-b2o8
```


