---
title: Configuring Parity Ethereum
---

Parity can be configured using either the [CLI options](#cli-options) or a [config file](#config-file). Should the CLI flags and the config file disagree about a setting, the CLI takes precedence.

You can list all CLI options by running `$parity --help`. The vast majority of CLI options map to a setting in the TOML file, for example `--mode-timeout 500` can be set by creating a config file:

```toml
[parity]
mode_timeout = 500
```

## Config File

Parity can be configured using a [TOML](https://github.com/toml-lang/toml) file. The file can be generated using the [Parity Config Generator](https://paritytech.github.io/parity-config-generator/). To start parity with a config file, the file needs to be located in:

* Windows: `%UserProfile%\AppData\Roaming\Parity\Ethereum\config.toml`
* Linux: `~/.local/share/io.parity.ethereum/config.toml`
* macOS: `$HOME/Library/Application Support/io.parity.ethereum/config.toml`

To use a custom path run `$ parity --config path/to/config.toml`.

## Default config.toml

The following is a representation of a configuration file with all default values.

```toml
[parity]
mode = "last"
mode_timeout = 300
mode_alarm = 3600
auto_update = "critical"
release_track = "current"
public_node = false
no_download = false
no_consensus = false
no_persistent_txqueue = false

chain = "homestead"
base_path = "$HOME/.local/share/io.parity.ethereum"
db_path = "$HOME/.local/share/io.parity.ethereum/chains"
keys_path = "$HOME/.local/share/io.parity.ethereum/keys"
identity = ""
light = false

[account]
unlock = ["0xdeadbeefcafe0000000000000000000000000000"]
password = ["/home/user/.safe/password.file"]
keys_iterations = 10240

[ui]
force = false
disable = false
port = 8180
interface = "127.0.0.1"
path = "$HOME/.local/share/io.parity.ethereum/signer"

[network]
port = 30303
min_peers = 25
max_peers = 50
nat = "any"
id = 1
bootnodes = []
discovery = true
warp = true
allow_ips = "all"
snapshot_peers = 0
max_pending_peers = 64
no_serve_light = false

reserved_only = false
reserved_peers = "./path_to_file"

[rpc]
disable = false
port = 8545
interface = "local"
cors = []
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "traces", "rpc", "shh", "shh_pubsub"]
hosts = ["none"]

[websockets]
disable = false
port = 8546
interface = "local"
origins = ["none"]
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "traces", "rpc", "shh", "shh_pubsub"]
hosts = ["none"]

[ipc]
disable = false
path = "$HOME/.local/share/io.parity.ethereum/jsonrpc.ipc"
apis = ["web3", "eth", "pubsub", "net", "parity", "parity_pubsub", "parity_accounts", "traces", "rpc", "shh", "shh_pubsub"]

[dapps]
disable = false
path = "$HOME/.local/share/io.parity.ethereum/dapps"

[secretstore]
disable = false
nodes = []
http_interface = "local"
http_port = 8082
interface = "local"
port = 8083
path = "$HOME/.local/share/io.parity.ethereum/secretstore"

[ipfs]
enable = false
port = 5001
interface = "local"
cors = []
hosts = ["none"]

[mining]
author = "0xdeadbeefcafe0000000000000000000000000001"
engine_signer = "0xdeadbeefcafe0000000000000000000000000001"
force_sealing = true
reseal_on_txs = "all"
reseal_min_period = 4000
reseal_max_period = 60000
work_queue_size = 20
relay_set = "cheap"
usd_per_tx = "0.0025"
usd_per_eth = "auto"
price_update_period = "hourly"
gas_floor_target = "4700000"
gas_cap = "6283184"
tx_queue_size = 8192
tx_queue_gas = "off"
tx_queue_strategy = "gas_factor"
tx_queue_ban_count = 1
tx_queue_ban_time = 180 #s
tx_gas_limit = "6283184"
tx_time_limit = 100 #ms
extra_data = "Parity"
remove_solved = false
notify_work = ["http://localhost:3001"]
refuse_service_transactions = false

[footprint]
tracing = "auto"
pruning = "auto"
pruning_history = 64
pruning_memory = 32
cache_size_db = 128
cache_size_blocks = 8
cache_size_queue = 40
cache_size_state = 25
cache_size = 128 # Overrides above caches with total size
fast_and_loose = false
db_compaction = "ssd"
fat_db = "auto"
scale_verifiers = true
num_verifiers = 6

[snapshots]
disable_periodic = false

[vm]
jit = false

[misc]
logging = "own_tx=trace"
log_file = "/var/log/parity.log"
color = true
```

## Presets
Parity can be launched with a [preset configuration file](https://github.com/paritytech/parity/tree/1d9542fe88044d0831471510beb23626050f1bbf/parity/cli/presets) using `--config` flag with one of the following value:
- `dev`: uses [dev chain specifications](https://wiki.parity.io/Private-development-chain.html) with [Instant-seal](https://wiki.parity.io/Pluggable-Consensus.html#instant-seal) consensus engine. The gas price is set to 0.
- `dev-insecure`: uses the same configuration as `dev`, plus sets the flag `no_consensus`, allows all RPC APIs and accepts all RPC interfaces and hosts, as well as all IPFS hosts. 
- `insecure`: uses the Mainnet default configuration, plus sets the flag `no_consensus`, allows all RPC APIs and accepts all RPC interfaces and hosts, as well as all IPFS hosts. 
- `mining`: uses the Mainnet default configuration, plus increases the number of peers to min 50 and max 100, it disables the Dapps and IPC interface. It forces the sealing of blocks with a minimum of 4 seconds interval, forces the reseal for any new transaction (external or local), reduces the transaction queue size to 2048 while increasing the cache size to 256 MB and setting the `trace` logging level for the `miner` and `own_tx` modules.
- `non-standard-ports`: sets the client to listen to the port 30305 and 8645 for RPC connections.

## CLI Options for Parity Ethereum client
```bash
Operating Options:
    --no-download
        Normally new releases will be downloaded ready for
        updating. This disables it. Not recommended.

    --no-consensus
        Force the binary to run even if there are known issues
        regarding consensus. Not recommended.

    --light
        Experimental: run in light client mode. Light clients
        synchronize a bare minimum of data and fetch necessary
        data on-demand from the network. Much lower in storage,
        potentially higher in bandwidth. Has no effect with
        subcommands.

    --no-hardcoded-sync
        By default, if there is no existing database the light
        client will automatically jump to a block hardcoded in
        the chain's specifications. This disables this feature.

    --force-direct
        Run the originally installed version of Parity, ignoring
        any updates that have since been installed.

    --mode=[MODE]
        Set the operating mode. MODE can be one of: last - Uses
        the last-used mode, active if none; active - Parity
        continuously syncs the chain; passive - Parity syncs
        initially, then sleeps and wakes regularly to resync;
        dark - Parity syncs only when the JSON-RPC is active;
        offline - Parity doesn't sync. (default: last)

    --mode-timeout=[SECS]
        Specify the number of seconds before inactivity timeout
        occurs when mode is dark or passive (default: 300)

    --mode-alarm=[SECS]
        Specify the number of seconds before auto sleep reawake
        timeout occurs when mode is passive (default: 3600)

    --auto-update=[SET]
        Set a releases set to automatically update and install.
        SET can be one of: all - All updates in the our release
        track; critical - Only consensus/security updates; none -
        No updates will be auto-installed. (default: critical)

    --auto-update-delay=[NUM]
        Specify the maximum number of blocks used for randomly
        delaying updates. (default: 100)

    --auto-update-check-frequency=[NUM]
        Specify the number of blocks between each auto-update
        check. (default: 20)

    --release-track=[TRACK]
        Set which release track we should use for updates. TRACK
        can be one of: stable - Stable releases; beta - Beta
        releases; nightly - Nightly releases (unstable); testing
        - Testing releases (do not use); current - Whatever track
        this executable was released on. (default: current)

    --chain=[CHAIN]
        Specify the blockchain type. CHAIN may be either a JSON
        chain specification file or olympic, frontier, homestead,
        mainnet, morden, ropsten, classic, expanse, tobalaba,
        musicoin, ellaism, easthub, social, testnet, kovan or
        dev. (default: foundation)

    --keys-path=[PATH]
        Specify the path for JSON key files to be found (default:
        $BASE/keys)

    --identity=[NAME]
        Specify your node's name. (default: )

    -d, --base-path=[PATH]
        Specify the base data storage path.

    --db-path=[PATH]
        Specify the database directory path

Convenience Options:
    --unsafe-expose
        All servers will listen on external interfaces and will
        be remotely accessible. It's equivalent with setting the
        following: --[ws,jsonrpc,ui,ipfs
        -api,secretstore,stratum,dapps,secretstore-
        http]-interface=all
         
        --*-hosts=all    This option is UNSAFE and should be used
        with great care!

    -c, --config=[CONFIG]
        Specify a configuration. CONFIG may be either a
        configuration file or a preset: dev, insecure, dev
        -insecure, mining, or non-standard-ports. (default:
        $BASE/config.toml)

    --ports-shift=[SHIFT]
        Add SHIFT to all port numbers Parity is listening on.
        Includes network port and all servers (HTTP JSON-RPC,
        WebSockets JSON-RPC, IPFS, SecretStore). (default: 0)

Account Options:
    --no-hardware-wallets
        Disables hardware wallet support.

    --fast-unlock
        Use drastically faster unlocking mode. This setting
        causes raw secrets to be stored unprotected in memory, so
        use with care.

    --keys-iterations=[NUM]
        Specify the number of iterations to use when deriving key
        from the password (bigger is more secure) (default:
        10240)

    --accounts-refresh=[TIME]
        Specify the cache time of accounts read from disk. If you
        manage thousands of accounts set this to 0 to disable
        refresh. (default: 5)

    --unlock=[ACCOUNTS]
        Unlock ACCOUNTS for the duration of the execution.
        ACCOUNTS is a comma-delimited list of addresses. Implies
        --no-ui.

    --password=[FILE]...
        Provide a file containing a password for unlocking an
        account. Leading and trailing whitespace is trimmed.
        (default: [])

Private Transactions Options:
    --private-tx-enabled
        Enable private transactions.

    --private-signer=[ACCOUNT]
        Specify the account for signing public transaction
        created upon verified private transaction.

    --private-validators=[ACCOUNTS]
        Specify the accounts for validating private transactions.
        ACCOUNTS is a comma-delimited list of addresses.

    --private-account=[ACCOUNT]
        Specify the account for signing requests to secret store.

    --private-sstore-url=[URL]
        Specify secret store URL used for encrypting private
        transactions.

    --private-sstore-threshold=[NUM]
        Specify secret store threshold used for encrypting
        private transactions.

    --private-passwords=[FILE]...
        Provide a file containing passwords for unlocking
        accounts (signer, private account, validators).

UI Options:
    --ui-path=[PATH]
        Specify directory where Trusted UIs tokens should be
        stored. (default: $BASE/signer)

Networking Options:
    --no-warp
        Disable syncing from the snapshot over the network.

    --no-discovery
        Disable new peer discovery.

    --reserved-only
        Connect only to reserved nodes.

    --no-ancient-blocks
        Disable downloading old blocks after snapshot restoration
        or warp sync. Not recommended.

    --no-serve-light
        Disable serving of light peers.

    --warp-barrier=[NUM]
        When warp enabled never attempt regular sync before
        warping to block NUM.

    --port=[PORT]
        Override the port on which the node should listen.
        (default: 30303)

    --interface=[IP]
        Network interfaces. Valid values are 'all', 'local' or
        the ip of the interface you want parity to listen to.
        (default: all)

    --min-peers=[NUM]
        Try to maintain at least NUM peers.

    --max-peers=[NUM]
        Allow up to NUM peers.

    --snapshot-peers=[NUM]
        Allow additional NUM peers for a snapshot sync. (default:
        0)

    --nat=[METHOD]
        Specify method to use for determining public address.
        Must be one of: any, none, upnp, extip:<IP>. (default:
        any)

    --allow-ips=[FILTER]
        Filter outbound connections. Must be one of: private -
        connect to private network IP addresses only; public -
        connect to public network IP addresses only; all -
        connect to any IP address. (default: all)

    --max-pending-peers=[NUM]
        Allow up to NUM pending connections. (default: 64)

    --network-id=[INDEX]
        Override the network identifier from the chain we are on.

    --bootnodes=[NODES]
        Override the bootnodes from our chain. NODES should be
        comma-delimited enodes.

    --node-key=[KEY]
        Specify node secret key, either as 64-character hex
        string or input to SHA3 operation.

    --reserved-peers=[FILE]
        Provide a file containing enodes, one per line. These
        nodes will always have a reserved slot on top of the
        normal maximum peers.

API and Console Options – HTTP JSON-RPC:
    --no-jsonrpc
        Disable the HTTP JSON-RPC API server.

    --jsonrpc-port=[PORT]
        Specify the port portion of the HTTP JSON-RPC API server.
        (default: 8545)

    --jsonrpc-interface=[IP]
        Specify the hostname portion of the HTTP JSON-RPC API
        server, IP should be an interface's IP address, or all
        (all interfaces) or local. (default: local)

    --jsonrpc-apis=[APIS]
        Specify the APIs available through the HTTP JSON-RPC
        interface using a comma-delimited list of API names.
        Possible names are: all, safe, web3, net, eth, pubsub,
        personal, signer, parity, parity_pubsub, parity_accounts,
        parity_set, traces, rpc, secretstore, shh, shh_pubsub.
        You can also disable a specific API by putting '-' in the
        front, example: all,-personal. 'safe' enables the
        following APIs: web3, net, eth, pubsub, parity,
        parity_pubsub, traces, rpc, shh, shh_pubsub (default:
        web3,eth,pubsub,net,parity,private,parity_pubsub,traces,r
        pc,shh,shh_pubsub)

    --jsonrpc-hosts=[HOSTS]
        List of allowed Host header values. This option will
        validate the Host header sent by the browser, it is
        additional security against some attack vectors. Special
        options: "all", "none",. (default: none)

    --jsonrpc-threads=[THREADS]
        Turn on additional processing threads in all HTTP JSON
        -RPC servers. Setting this to non-zero value allows
        parallel execution of cpu-heavy queries. (default: 4)

    --jsonrpc-cors=[URL]
        Specify CORS header for HTTP JSON-RPC API responses.
        Special options: "all", "none". (default: none)

    --jsonrpc-server-threads=[NUM]
        Enables multiple threads handling incoming connections
        for HTTP JSON-RPC server.

    --jsonrpc-max-payload=[MB]
        Specify maximum size for HTTP JSON-RPC requests in
        megabytes.

API and Console Options – WebSockets:
    --no-ws
        Disable the WebSockets JSON-RPC server.

    --ws-port=[PORT]
        Specify the port portion of the WebSockets JSON-RPC
        server. (default: 8546)

    --ws-interface=[IP]
        Specify the hostname portion of the WebSockets JSON-RPC
        server, IP should be an interface's IP address, or all
        (all interfaces) or local. (default: local)

    --ws-apis=[APIS]
        Specify the JSON-RPC APIs available through the
        WebSockets interface using a comma-delimited list of API
        names. Possible names are: all, safe, web3, net, eth,
        pubsub, personal, signer, parity, parity_pubsub,
        parity_accounts, parity_set, traces, rpc, secretstore,
        shh, shh_pubsub. You can also disable a specific API by
        putting '-' in the front, example: all,-personal. 'safe'
        enables the following APIs: web3, net, eth, pubsub,
        parity, parity_pubsub, traces, rpc, shh, shh_pubsub
        (default:
        web3,eth,pubsub,net,parity,parity_pubsub,private,traces,r
        pc,shh,shh_pubsub)

    --ws-origins=[URL]
        Specify Origin header values allowed to connect. Special
        options: "all", "none". (default: parity://*,chrome
        -extension://*,moz-extension://*)

    --ws-hosts=[HOSTS]
        List of allowed Host header values. This option will
        validate the Host header sent by the browser, it is
        additional security against some attack vectors. Special
        options: "all", "none". (default: none)

    --ws-max-connections=[CONN]
        Maximum number of allowed concurrent WebSockets JSON-RPC
        connections. (default: 100)

API and Console Options – IPC:
    --no-ipc
        Disable JSON-RPC over IPC service.

    --ipc-path=[PATH]
        Specify custom path for JSON-RPC over IPC service.
        (default: $BASE/jsonrpc.ipc)

    --ipc-apis=[APIS]
        Specify custom API set available via JSON-RPC over IPC
        using a comma-delimited list of API names. Possible names
        are: all, safe, web3, net, eth, pubsub, personal, signer,
        parity, parity_pubsub, parity_accounts, parity_set,
        traces, rpc, secretstore, shh, shh_pubsub. You can also
        disable a specific API by putting '-' in the front,
        example: all,-personal. 'safe' enables the following
        APIs: web3, net, eth, pubsub, parity, parity_pubsub,
        traces, rpc, shh, shh_pubsub (default:
        web3,eth,pubsub,net,parity,parity_pubsub,parity_accounts,
        private,traces,rpc,shh,shh_pubsub)

API and Console Options – IPFS:
    --ipfs-api
        Enable IPFS-compatible HTTP API.

    --ipfs-api-port=[PORT]
        Configure on which port the IPFS HTTP API should listen.
        (default: 5001)

    --ipfs-api-interface=[IP]
        Specify the hostname portion of the IPFS API server, IP
        should be an interface's IP address or local. (default:
        local)

    --ipfs-api-hosts=[HOSTS]
        List of allowed Host header values. This option will
        validate the Host header sent by the browser, it is
        additional security against some attack vectors. Special
        options: "all", "none". (default: none)

    --ipfs-api-cors=[URL]
        Specify CORS header for IPFS API responses. Special
        options: "all", "none". (default: none)

Secret Store Options:
    --no-secretstore
        Disable Secret Store functionality.

    --no-secretstore-http
        Disable Secret Store HTTP API.

    --no-secretstore-auto-migrate
        Do not run servers set change session automatically when
        servers set changes. This option has no effect when
        servers set is read from configuration file.

    --secretstore-acl-contract=[SOURCE]
        Secret Store permissioning contract address source: none,
        registry (contract address is read from
        'secretstore_acl_checker' entry in registry) or address.
        (default: registry)

    --secretstore-contract=[SOURCE]
        Secret Store Service contract address source: none,
        registry (contract address is read from
        'secretstore_service' entry in registry) or address.

    --secretstore-srv-gen-contract=[SOURCE]
        Secret Store Service server key generation contract
        address source: none, registry (contract address is read
        from 'secretstore_service_srv_gen' entry in registry) or
        address.

    --secretstore-srv-retr-contract=[SOURCE]
        Secret Store Service server key retrieval contract
        address source: none, registry (contract address is read
        from 'secretstore_service_srv_retr' entry in registry) or
        address.

    --secretstore-doc-store-contract=[SOURCE]
        Secret Store Service document key store contract address
        source: none, registry (contract address is read from
        'secretstore_service_doc_store' entry in registry) or
        address.

    --secretstore-doc-sretr-contract=[SOURCE]
        Secret Store Service document key shadow retrieval
        contract address source: none, registry (contract address
        is read from 'secretstore_service_doc_sretr' entry in
        registry) or address.

    --secretstore-nodes=[NODES]
        Comma-separated list of other secret store cluster nodes
        in form NODE_PUBLIC_KEY_IN_HEX@NODE_IP_ADDR:NODE_PORT.
        (default: )

    --secretstore-server-set-contract=[SOURCE]
        Secret Store server set contract address source: none,
        registry (contract address is read from
        'secretstore_server_set' entry in registry) or address.
        (default: registry)

    --secretstore-interface=[IP]
        Specify the hostname portion for listening to Secret
        Store Key Server internal requests, IP should be an
        interface's IP address, or local. (default: local)

    --secretstore-port=[PORT]
        Specify the port portion for listening to Secret Store
        Key Server internal requests. (default: 8083)

    --secretstore-http-interface=[IP]
        Specify the hostname portion for listening to Secret
        Store Key Server HTTP requests, IP should be an
        interface's IP address, or local. (default: local)

    --secretstore-http-port=[PORT]
        Specify the port portion for listening to Secret Store
        Key Server HTTP requests. (default: 8082)

    --secretstore-path=[PATH]
        Specify directory where Secret Store should save its
        data. (default: $BASE/secretstore)

    --secretstore-secret=[SECRET]
        Hex-encoded secret key of this node.

    --secretstore-admin=[PUBLIC]
        Hex-encoded public key of secret store administrator.

Sealing/Mining Options:
    --force-sealing
        Force the node to author new blocks as if it were always
        sealing/mining.

    --reseal-on-uncle
        Force the node to author new blocks when a new uncle
        block is imported.

    --remove-solved
        Move solved blocks from the work package queue instead of
        cloning them. This gives a slightly faster import speed,
        but means that extra solutions submitted for the same
        work package will go unused.

    --tx-queue-no-unfamiliar-locals
        Local transactions sent through JSON-RPC (HTTP,
        WebSockets, etc) will be treated as 'external' if the
        sending account is unknown.

    --refuse-service-transactions
        Always refuse service transactions.

    --infinite-pending-block
        Pending block will be created with maximal possible gas
        limit and will execute all transactions in the queue.
        Note that such block is invalid and should never be
        attempted to be mined.

    --no-persistent-txqueue
        Don't save pending local transactions to disk to be
        restored whenever the node restarts.

    --stratum
        Run Stratum server for miner push notification.

    --reseal-on-txs=[SET]
        Specify which transactions should force the node to
        reseal a block. SET is one of: none - never reseal on new
        transactions; own - reseal only on a new local
        transaction; ext - reseal only on a new external
        transaction; all - reseal on all new transactions.
        (default: own)

    --reseal-min-period=[MS]
        Specify the minimum time between reseals from incoming
        transactions. MS is time measured in milliseconds.
        (default: 2000)

    --reseal-max-period=[MS]
        Specify the maximum time since last block to enable
        force-sealing. MS is time measured in milliseconds.
        (default: 120000)

    --work-queue-size=[ITEMS]
        Specify the number of historical work packages which are
        kept cached lest a solution is found for them later. High
        values take more memory but result in fewer unusable
        solutions. (default: 20)

    --relay-set=[SET]
        Set of transactions to relay. SET may be: cheap - Relay
        any transaction in the queue (this may include invalid
        transactions); strict - Relay only executed transactions
        (this guarantees we don't relay invalid transactions, but
        means we relay nothing if not mining); lenient - Same as
        strict when mining, and cheap when not. (default: cheap)

    --usd-per-tx=[USD]
        Amount of USD to be paid for a basic transaction. The
        minimum gas price is set accordingly. (default: 0.0001)

    --usd-per-eth=[SOURCE]
        USD value of a single ETH. SOURCE may be either an amount
        in USD, a web service or 'auto' to use each web service
        in turn and fallback on the last known good value.
        (default: auto)

    --price-update-period=[T]
        T will be allowed to pass between each gas price update.
        T may be daily, hourly, a number of seconds, or a time
        string of the form "2 days", "30 minutes" etc.. (default:
        hourly)

    --gas-floor-target=[GAS]
        Amount of gas per block to target when sealing a new
        block. (default: 4700000)

    --gas-cap=[GAS]
        A cap on how large we will raise the gas limit per block
        due to transaction volume. (default: 6283184)

    --tx-queue-mem-limit=[MB]
        Maximum amount of memory that can be used by the
        transaction queue. Setting this parameter to 0 disables
        limiting. (default: 4)

    --tx-queue-size=[LIMIT]
        Maximum amount of transactions in the queue (waiting to
        be included in next block). (default: 8192)

    --tx-queue-per-sender=[LIMIT]
        Maximum number of transactions per sender in the queue.
        By default it's 1% of the entire queue, but not less than
        16.

    --tx-queue-gas=[LIMIT]
        Maximum amount of total gas for external transactions in
        the queue. LIMIT can be either an amount of gas or 'auto'
        or 'off'. 'auto' sets the limit to be 20x the current
        block gas limit. (default: off)

    --tx-queue-strategy=[S]
        Prioritization strategy used to order transactions in the
        queue. S may be: gas_price - Prioritize txs with high gas
        price (default: gas_price)

    --stratum-interface=[IP]
        Interface address for Stratum server. (default: local)

    --stratum-port=[PORT]
        Port for Stratum server to listen on. (default: 8008)

    --min-gas-price=[STRING]
        Minimum amount of Wei per GAS to be paid for a
        transaction to be accepted for mining. Overrides --usd
        -per-tx.

    --gas-price-percentile=[PCT]
        Set PCT percentile gas price value from last 100 blocks
        as default gas price when sending transactions. (default:
        50)

    --poll-lifetime=[S]
        Set the lifetime of the internal index filter to S
        seconds. (default: 60)

    --author=[ADDRESS]
        Specify the block author (aka "coinbase") address for
        sending block rewards from sealed blocks. NOTE: MINING
        WILL NOT WORK WITHOUT THIS OPTION.

    --engine-signer=[ADDRESS]
        Specify the address which should be used to sign
        consensus messages and issue blocks. Relevant only to
        non-PoW chains.

    --tx-gas-limit=[GAS]
        Apply a limit of GAS as the maximum amount of gas a
        single transaction may have for it to be mined.

    --tx-time-limit=[MS]
        Maximal time for processing single transaction. If
        enabled senders of transactions offending the limit will
        get other transactions penalized.

    --extra-data=[STRING]
        Specify a custom extra-data for authored blocks, no more
        than 32 characters.

    --notify-work=[URLS]
        URLs to which work package notifications are pushed. URLS
        should be a comma-delimited list of HTTP URLs.

    --stratum-secret=[STRING]
        Secret for authorizing Stratum server for peers.

Internal Options:
    --can-restart
        Executable will auto-restart if exiting with 69

Miscellaneous Options:
    --no-color
        Don't use terminal color codes in output.

    -v, --version
        Show information about version.

    --no-config
        Don't load a configuration file.

    --ntp-servers=[HOSTS]
        Comma separated list of NTP servers to provide current
        time (host:port). Used to verify node health. Parity uses
        pool.ntp.org NTP servers; consider joining the pool:
        http://www.pool.ntp.org/join.html (default:
        0.parity.pool.ntp.org:123,1.parity.pool.ntp.org:123,2.par
        ity.pool.ntp.org:123,3.parity.pool.ntp.org:123)

    -l, --logging=[LOGGING]
        Specify the logging level. Must conform to the same
        format as RUST_LOG.

    --log-file=[FILENAME]
        Specify a filename into which logging should be appended.

Footprint Options:
    --scale-verifiers
        Automatically scale amount of verifier threads based on
        workload. Not guaranteed to be faster.

    --tracing=[BOOL]
        Indicates if full transaction tracing should be enabled.
        Works only if client had been fully synced with tracing
        enabled. BOOL may be one of auto, on, off. auto uses last
        used value of this option (off if it does not exist).
        (default: auto)

    --pruning=[METHOD]
        Configure pruning of the state/storage trie. METHOD may
        be one of auto, archive, fast: archive - keep all state
        trie data. No pruning. fast - maintain journal overlay.
        Fast but 50MB used. auto - use the method most recently
        synced or default to fast if none synced. (default: auto)

    --pruning-history=[NUM]
        Set a minimum number of recent states to keep in memory
        when pruning is active. (default: 64)

    --pruning-memory=[MB]
        The ideal amount of memory in megabytes to use to store
        recent states. As many states as possible will be kept
        within this limit, and at least --pruning-history states
        will always be kept. (default: 32)

    --cache-size-db=[MB]
        Override database cache size. (default: 128)

    --cache-size-blocks=[MB]
        Specify the preferred size of the blockchain cache in
        megabytes. (default: 8)

    --cache-size-queue=[MB]
        Specify the maximum size of memory to use for block
        queue. (default: 40)

    --cache-size-state=[MB]
        Specify the maximum size of memory to use for the state
        cache. (default: 25)

    --db-compaction=[TYPE]
        Database compaction type. TYPE may be one of: ssd -
        suitable for SSDs and fast HDDs; hdd - suitable for slow
        HDDs; auto - determine automatically. (default: auto)

    --fat-db=[BOOL]
        Build appropriate information to allow enumeration of all
        accounts and storage keys. Doubles the size of the state
        database. BOOL may be one of on, off or auto. (default:
        auto)

    --cache-size=[MB]
        Set total amount of discretionary memory to use for the
        entire system, overrides other cache and queue options.

    --num-verifiers=[INT]
        Amount of verifier threads to use or to begin with, if
        verifier auto-scaling is enabled.

Import/export Options:
    --no-seal-check
        Skip block seal check.

Snapshot Options:
    --no-periodic-snapshot
        Disable automated snapshots which usually occur once
        every 10000 blocks.

Whisper Options:
    --whisper
        Enable the Whisper network.

    --whisper-pool-size=[MB]
        Target size of the whisper message pool in megabytes.
        (default: 10)
```
