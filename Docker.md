---
title: Docker
---

Build and run OpenEthereum anywhere with Docker:

- [Pick a container](#pick-a-container)
- [Run container](#run-container)
- [Configure OpenEthereum](#configure-openethereum)
    - [Open ports](#open-ports)
    - [Pass arguments](#pass-arguments)
    - [Config file](#config-file)
    - [Persistent data directory](#persistent-data-directory)
    - [Run in background](#run-in-background)
- [OpenEthereum Deploy Scripts](#openethereum-deploy-scripts)
    - [Prepare the Node](#prepare-the-node)
    - [Launching OpenEthereum](#launching-openethereum)
    - [More Options](#more-options)
- [Further reading](#further-reading)


### Pick a container

Docker containers for OpenEthereum are available via [Docker Hub](https://hub.docker.com/r/openethereum/openethereum/):

```bash
$ docker search openethereum/openethereum
NAME                        DESCRIPTION                STARS               OFFICIAL            AUTOMATED
openethereum/openethereum                              1
```

To get a list of available versions, use `curl` and `jq`:

```bash
$ curl -sS 'https://registry.hub.docker.com/v2/repositories/openethereum/openethereum/tags/'  | jq '."results"[]["name"]' | sort
"latest"
"nightly"
"v3.0.1"
```

To get the latest stable release, run:

```bash
$ docker pull openethereum/openethereum:latest
```

### Run container

To run OpenEthereum with an interactive pseudo-tty shell, run:

```bash
$ docker run -ti openethereum/openethereum:v3.0.0
```

### Configure OpenEthereum

OpenEthereum can be configured using either the [CLI options or a config file](Configuring-OpenEthereum). Should the CLI flags and the config file disagree about a setting, the CLI takes precedence. You can list all CLI options by running:

```bash
$ docker run openethereum/openethereum:v3.0.0 --help
```

For Docker specific options, please refer to the [Docker documentation](https://docs.docker.com/engine/), or run `docker --help` or `docker run --help`.

##### Open ports

To publish OpenEthereum's ports to the host machine, use the `-p` option:

```bash
$ docker run -ti -p 8545:8545 -p 8546:8546 -p 30303:30303 -p 30303:30303/udp openethereum/openethereum:v3.0.0 --jsonrpc-interface all
```

For example, this will expose the HTTP and WebSockets JSONRPC APIs, and the listen port to the host. Now you can send RPC calls from the Docker host computer.

To enable external discovery (for example for PoA sealing nodes), specify the external IP by appending the flag `--nat extip:133.3.3.37`, where `133.3.3.37` is to be replaced by your actual external IP of the host.

##### Pass arguments

To pass further operating options to OpenEthereum, simply append them to the `docker run` command:

```bash
$ docker run -ti openethereum/openethereum:v3.0.0 --no-discovery
```

In this case, it disables the discovery.

##### Config file

For more complex node configurations, a TOML config file can be created and attached to the docker container.

```bash
$ mkdir -p ~/.local/share/openethereum/docker/
$ touch ~/.local/share/openethereum/docker/config.toml
```

To mount the configuration, use the `docker run -v` option:

```bash
$ docker run -ti -v ~/.local/share/openethereum/docker/:/home/openethereum/.local/share/openethereum/ openethereum/openethereum:v3.0.0 --config /home/parity/.local/share/openethereum/config.toml
```

This will mount `~/.local/share/openethereum/docker/` of the host machine at `/home/openethereum/.local/share/openethereum/` inside the docker container. Therefore, the config file will be available via `--config /home/openethereum/.local/share/openethereum/config.toml`.

##### Persistent data directory

In case you need to persist the blockchain files, keys etc., you should run the image with the `--base-path` option and then mount it, e.g.:

```
$ docker run -ti -v ~/.local/share/openethereum/docker/:/home/openethereum/.local/share/openethereum/ openethereum/openethereum:v3.0.0 --base-path /home/openethereum/.local/share/openethereum/
```

This will expose the whole data dir to the host machine at `~/.local/share/openethereum/docker/`.

Windows machines don't support unix permissions, which means you will likely experience errors when mounting a local volume as a non-root user. One workaround for this is to create a volume using:

```
docker volume create --driver=local --opt o=uid=1000 --opt type=tmpfs --opt device=tmpfs openethereumdb
```
This ensures that the volume has the correct permissions to give the `openethereum` user access to it.

Your can then mount the volume with:

```
$ docker run -ti -v openethereumdb:/home/openethereum/.local/share/openethereum/ openethereum/openethereum:v3.0.0 --base-path /home/openethereum/.local/share/openethereum/
```

##### Run in background

To run a detached OpenEthereum instance, use `docker run -d`:

```bash
$ docker run -d openethereum/openethereum:v3.0.0
245f312f3f39ad0a518091b1ee4cdc0c1f6d74fb9609395ed3fdcf43acae4b62
```

It will run OpenEthereum in background. `docker ps` shows the instance:

```bash
$ docker ps
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                          NAMES
245f312f3f39        openethereum/openethereum:v3.0.0   "/openethereum/openethereum"    7 seconds ago       Up 6 seconds        8080/tcp, 8180/tcp, 8545/tcp   epic_pike
```

To attach the container, use `docker attach`:

```bash
$ docker attach --sig-proxy=false 245f312f3f39
```

Disabling the signal proxy [allows to detach again](http://stackoverflow.com/a/22894096) with `CTRL`+`C`.

### OpenEthereum Deploy Scripts

A OpenEthereum deployment script generator is available at [paritytech/parity-deploy](https://github.com/openethereum/parity-deploy). It uses `docker` and `docker-compose`. On Ubuntu systems these will automatically be installed if not already present on the system.

Currently these scripts supports two types of chains, either [instant sealing](Pluggable-Consensus#instant-seal) for development and [authority round](Pluggable-Consensus#aura) for proof of authority with multiple validators.

##### Prepare the node

Some examples of using the script are:

- A single node instant seal node:

  ```bash
  $ ./parity-deploy.sh --name testchain --config instantseal
  ```

- A three node proof of authority chain (using the Aura consensus algorithm):

  ```bash
  $ ./parity-deploy.sh --name testchain --config aura --nodes 3
  ```

##### Launching OpenEthereum

Once the configuration is created you just need to run the docker-compose command to launch the machine or machines. This can be done via:

```bash
$ docker-compose up -d
```

You will then be able to see the logs by running:

```bash
$ docker-compose logs -f
```

In these logs you should see a token being generated to login to OpenEthereum. Alternatively you can run the command:

```bash
$ docker-compose logs | grep token
```

Once you are logged into the web interface if you go to _Add Accounts_, then select the option recovery phrase and enter the account recovery phrase as password. You now have an account with lots of ether to send around.

##### More options

You can also include extra nodes (e.g. [ethstats monitoring](https://github.com/cubedro/eth-net-intelligence-api)) by including the docker-compose configuration in `include/docker-compose.yml`. To add Ethstats monitoring you would need to include this in the file:

```
  monitor:
    image: buythewhale/ethstats_monitor
    volumes:
      - ./monitor/app.json:/home/ethnetintel/eth-net-intelligence-api/app.json:ro
  dashboard:
    image: buythewhale/ethstats
    volumes:
      - ./dashboard/ws_secret.json:/eth-netstats/ws_secret.json:ro
    ports:
      - 3001:3000
```

### Further reading

- [Running a OpenEthereum node in Docker and connect safely](https://medium.com/@preitsma/setting-up-a-parity-ethereum-node-in-docker-and-connect-safely-f881faa17686).
- [How to quickly upgrade an OpenEthereum node using Docker](https://medium.com/decentralized-capital/how-to-quickly-upgrade-an-ethereum-parity-node-using-docker-e170fa2a2045).
- [Docker compose network with 3 authorities and netstats dashboard](https://github.com/dstarcev/parity-poa-playground).
- [Docker compose for running integration tests](https://github.com/illya13/parity-poa).
- [Building OpenEthereum using Docker](Setup#Building-using-Docker).
- [OpenEthereum on Docker Hub](https://hub.docker.com/r/openethereum/openethereum/).
- [View the Dockerfile here](https://github.com/openethereum/openethereum/blob/master/docker/ubuntu/Dockerfile).
