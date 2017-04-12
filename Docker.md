### Pick an image

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

```bash
$ curl -sS 'https://registry.hub.docker.com/v2/repositories/ethcore/parity/tags/'  | jq '."results"[]["name"]' |sort
"beta"
"latest"
"master"
"nightly"
"stable"
"v1.5.12"
"v1.6.6"
```

```bash
$ docker pull ethcore/parity:beta
```

### Run container

```bash
$ docker run -ti ethcore/parity:beta
```

### Configure parity

##### Open ports

```bash
$ docker run -ti -p 8180:8180 -p 8545:8545 -p 30303:30303 ethcore/parity:beta
```

##### Pass arguments

```bash
$ docker run -ti ethcore/parity:beta --no-ui --no-dapps --no-discovery
```

##### Config file

```bash
$ mkdir -p ~/.local/share/io.parity.ethereum/docker/
$ touch ~/.local/share/io.parity.ethereum/docker/config.toml
```

[Config File Generator](#)

```bash
$ docker run -ti -v ~/.local/share/io.parity.ethereum/docker/:/mnt/ ethcore/parity:beta -c /mnt/config.toml
```

###### Run in background (detach)

```bash
$ docker run -dti ethcore/parity:beta
245f312f3f39ad0a518091b1ee4cdc0c1f6d74fb9609395ed3fdcf43acae4b62
```

```bash
$ docker ps
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                          NAMES
245f312f3f39        ethcore/parity:beta   "/parity/parity"    7 seconds ago       Up 6 seconds        8080/tcp, 8180/tcp, 8545/tcp   epic_pike
```

```bash
$ docker attach --sig-proxy=false 245f312f3f39
```

`CTRL`+`C` will detach.
