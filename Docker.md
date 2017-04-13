Build and run Parity anywhere with Docker:

- [Pick a container](#pick-a-container)
- [Run container](#run-container)
- [Configure parity](#configure-parity)
    - [Open ports](#open-ports)
    - [Pass arguments](#pass-arguments)
    - [Config file](#config-file)
    - [Persistent data directory](#persistent-data-directory)
    - [Run in background](#run-in-background)
- [Further reading](#further-reading)

### Pick a container

Docker containers for Parity are available via [Docker Hub](https://hub.docker.com/r/ethcore/parity/):

```bash
$ docker search ethcore/parity
NAME                     DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
ethcore/parity           Parity is Ethcore's initial fully-featured...   14                   [OK]
ethcore/parity-poa                                                       2
ethcore/parity-arm       Parity is Ethcore's initial fully-featured...   1                    [OK]
ethcore/parity-centos    Parity is Ethcore's initial fully-featured...   1                    [OK]
ethcore/parity-jit       Parity is Ethcore's initial fully-featured...   1                    [OK]
ethcore/parity-aarch64   Parity is Ethcore's initial fully-featured...   1                    [OK]
ethcore/parity-dev       Parity is Ethcore's initial fully-featured...   0
```

To get a list of available versions, use `curl` and `jq`:

```bash
$ curl -sS 'https://registry.hub.docker.com/v2/repositories/ethcore/parity/tags/'  | jq '."results"[]["name"]' | sort
"beta"
"latest"
"master"
"nightly"
"stable"
"v1.5.12"
"v1.6.6"
```

In general, you want one of `stable`, `beta`, or `nightly`, which always pulls the latest version from the according release channel, e.g., for `beta` run:

```bash
$ docker pull ethcore/parity:beta
```

### Run container

To run Parity with an interactive pseudo-tty shell, run:

```bash
$ docker run -ti ethcore/parity:beta
```

### Configure parity

Parity can be configured using either the [CLI options or a config file](Configuring-Parity). Should the CLI flags and the config file disagree about a setting, the CLI takes precedence. You can list all CLI options by running:

```bash
$ docker run ethcore/parity:beta --help
```

For Docker specific options, please refer to the [Docker documentation](https://docs.docker.com/engine/), or run `docker --help` or `docker run --help`.

##### Open ports

To publish Parity's ports to the host machine, use the `-p` option:

```bash
$ docker run -ti -p 8180:8180 -p 8545:8545 -p 30303:30303 ethcore/parity:beta
```

For example, this will expose the User Interface, the JSONRPC-API, and the listen port to the host. Now you can browse to http://localhost:8180 from the Docker host computer to access the Parity Wallet.

##### Pass arguments

To pass further operating options to Parity, simply append them to the `docker run` command:

```bash
$ docker run -ti ethcore/parity:beta --no-ui --no-dapps --no-discovery
```

In this case, it disables the Wallet, the DApps Server, and discovery.

##### Config file

For more complex node configurations, a TOML config file can be created and attached to the docker container.

```bash
$ mkdir -p ~/.local/share/io.parity.ethereum/docker/
$ touch ~/.local/share/io.parity.ethereum/docker/config.toml
```

You can use the [Config File Generator](https://paritytech.github.io/parity-config-generator/) to configure your Parity node and save it on your host's disk, i.e., at `~/.local/share/io.parity.ethereum/docker/config.toml`. To mount the configuration, use the `docker run -v` option:

```bash
$ docker run -ti -v ~/.local/share/io.parity.ethereum/docker/:/mnt/ ethcore/parity:beta --config /mnt/config.toml
```

This will mount `~/.local/share/io.parity.ethereum/docker/` of the host machine at `/mnt/` inside the docker container. Therefore, the config file will be available via `--config /mnt/config.toml`.

##### Persistent data directory

In case you need to persist the blockchain files, keys etc., you should run the image with the `--base-path` option and then mount it, e.g.:

```
$ docker run -ti -v ~/.local/share/io.parity.ethereum/docker/:/mnt/ ethcore/parity:beta --base-path /mnt/
```

This will expose the whole data dir to the host machine at `~/.local/share/io.parity.ethereum/docker/`.

##### Run in background

To run a detached Parity instance, use `docker run -d`:

```bash
$ docker run -d ethcore/parity:beta
245f312f3f39ad0a518091b1ee4cdc0c1f6d74fb9609395ed3fdcf43acae4b62
```

It will run Parity in background. `docker ps` shows the instance:

```bash
$ docker ps
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                          NAMES
245f312f3f39        ethcore/parity:beta   "/parity/parity"    7 seconds ago       Up 6 seconds        8080/tcp, 8180/tcp, 8545/tcp   epic_pike
```

To attach the container, use `docker attach`:

```bash
$ docker attach --sig-proxy=false 245f312f3f39
```

Disabling the signature proxy [allows to detach again](http://stackoverflow.com/a/22894096) with `CTRL`+`C`.

### Further reading

- [Running a Parity Ethereum node in Docker and connect safely](https://medium.com/@preitsma/setting-up-a-parity-ethereum-node-in-docker-and-connect-safely-f881faa17686).
- [How to quickly upgrade an Ethereum Parity node using Docker](https://medium.com/decentralized-capital/how-to-quickly-upgrade-an-ethereum-parity-node-using-docker-e170fa2a2045).
- [Docker compose network with 3 authorities and netstats dashboard](https://github.com/dstarcev/parity-poa-playground).
- [Docker compose for running integration tests](https://github.com/illya13/parity-poa).
- [Building Parity using Docker](Setup#Building-using-Docker).
- [Docker build for ARM64](https://github.com/paritytech/parity-snappy/wiki/Docker-build-for-ARM-ARM64).
- [Parity on Docker Hub](https://hub.docker.com/r/ethcore/parity/).
- [View the Dockerfile here](https://github.com/paritytech/parity/blob/master/docker/ubuntu/Dockerfile).
