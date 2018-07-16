---
title: Parity Ethereum CLI Sub-commands
---
## Summary
* [Export state](#export-state)
* [Export blocks](#export-blocks)
* [Import](#import)
* [Account new](#account-new)
* [Account list](#account-list)
* [Account import](#account-import)
* [Db kill](#db-kill)
* [Export hardcoded sync](#export-hardcoded-sync)
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
`parity export state ./export-state-file.bin`

## Export blocks

Export blocks into the given --chain database (default: foundation).
This command requires the chain to be synced with --fat-db on.

### Options:
- `--format <FORMAT>`    Export in a given format. FORMAT must be either 'hex' or 'binary'. (default: binary)
- `--from <BLOCK>`       Export from block BLOCK, which may be an index or hash.
- `--to <BLOCK>`         Export to (including) block BLOCK, which may be an index, hash or latest.

### Example:
`parity export blocks --chain kovan ./export-blocks-kovan.bin`

## Import

Import blockchain from a file to the given --chain database (default: foundation).

### Options:
- `--format <FORMAT>`    Import in a given format. FORMAT must be either 'hex' or 'binary'. (default: auto)

### Example: `parity import ./export-blocks-file.bin`

## Account new

Create a new account (and its associated key) for the given --chain (default: foundation).

### Example:
```bash
parity --chain kovan account new
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
parity --chain kovan account list
0x00780865dcc3c7dbffd943136107e70b8270286b
0x00dfc93112abd2578503b667b95491b101281f2b
```

## Account import

Import accounts from JSON UTC keystore files to the specified --chain (default mainnet).

### Example:
```bash
parity --chain kovan account list
0x00780865dcc3c7dbffd943136107e70b8270286b
0x00dfc93112abd2578503b667b95491b101281f2b


$ parity account import --chain kovan ./keystore-file.json 
1 account(s) imported

$ parity --chain kovan account list
0x003324332f7a9c2fb9536fcc4246a0bb4ea1b63f
0x00780865dcc3c7dbffd943136107e70b8270286b
0x00dfc93112abd2578503b667b95491b101281f2b
```

## Db kill

Clean the database of the given --chain (default: foundation).
### Example:
```
parity db kill --chain kovan
Database deleted.
```


## Export hardcoded sync

Print the hashed light clients' headers of the given --chain (default: foundation) in a JSON format.
To be used as hardcoded headers in a genesis file.

### Example:
```bash
parity export-hardcoded-sync --chain kovan

"hardcodedSync": {
    "header": "f90219a061d694007fbaca6e23e73e29c8c6a60099abc740ab7e27ae3cd1b9c6a47efef7a01dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347945a0b54d5dc17e0aadc383d2db43b0a0d3e029c4ca0a2f1bdabc1a72737de1c22a76cacc8fc162366904f759a99db7d6d19efee3090a0ac5f5b236e8977928a2ce43c7569ea5a74919643cb0b06d7540407b1ea1298f0a04356ddc5d77c83923a6541260308be167386e0969a608a017770c9e38091cfcab90100a00010002001009080011010141088000004000080081100000a002023000002204204801204084000c000010008000000000880080020c0000440200460000290005010c01c80800080004800100406003380000400402040000028084002a80087000008090a00200100544020019580022000000306100a0080100084020006809000e80000010000254810002000000a240050014200002002c10809202030006422022000203012000241089300080400000009001021020200012410348500028290230408100302000000058c0000020c08c20480081040020260004008481000080000800010010060020000e00020002140100a8988000004400201870b9af4a66df8038350a8018379d54483799eba845ab0426d984554482e45544846414e532e4f52472d3231323134313232a05eeccc80302d8fecca48a47be03654b5a41b5e5f296f271f910ebae631124f518890074810024c6c2b",
    "totalDifficulty": "3144406426008470895785",
    "CHTs": [
      "0x0eb474b7721727204978e92e27d31cddff56471911e424a4c8271c35f9c982cc",
      "0xe10e94515fb5ffb7ffa9bf50db4a959b3f50c2ff75e0b8bd5f5e038749e52a11",
      "0x816e7463af7b5d2fcb804ba55f09e8452182b0ba6c995a34e144245d76333d55",
      "0x3793af64c1ddc07ab61b2ba120034d91c02183ff788f07d3120fd4e6a48305b5",
      "0x14c6106a17e041032210bfa0ca80d11860a1c6d95175d55eff39f97b8d8acded",
      "0x396f832bfa3a9c494e9245471f0e65552613d87b6fe62128103590d95de72c2d",
...
      "0xb060979f095c170a776b2b50a1e2ab0ffea80f6e522753fa36ad6f106ee32e9f",
      "0x8f452e7cbd8a333ed04d819a143a8d3a75fe8c58418e7fc420bb2a717c0d4d2f",
      "0x37fe1b0cf156bfc07571569af210540be753777903a308d5707538fffed75b59",
      "0x6f0561d017cfc123b3f0d37b044e4f7231516b8731a1cab89afb569238643c33",
    ]
}
```


## Daemon

Launch Parity Ethereum as a daemon.

### Example:
`parity daemon ~/parity-daemon.pid --chain kovan`

## Snapshot

Make a snapshot of the database of the given --chain (default: foundation).

### Example:
`parity snapshot ~/kovan.snapshot --chain kovan`

### Options:
        --at <BLOCK>    Take a snapshot at the given block, which may be an index, hash, or "latest". Note that taking
                        snapshots at non-recent blocks will only work with --pruning archive

## Restore

Restore database of the given --chain (default: foundation) from snapshot.

### Example:
`parity restore ~/kovan.snapshot --chain kovan`

## Tools hash

Hash a file using Keccak-256 algorithm.

### Example:
```
parity tools hash ./some_file_to_hash.zip
aa7c3630b1c735777964b7a60ab1c2db9059d369ecb12f155778ca0f408b849c
```

## Signer new-token

Generate new token for the given --chain (default: foundation).

### Example:
```bash
parity signer new-token --chain kovan

xAtk-66Q1-lPu5-b2o8
```


