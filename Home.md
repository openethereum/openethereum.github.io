# Parity

### Quick Start with Docker

*NOTE*: Ensure you have docker to begin with.

```
docker run -it ubuntu bash
```

This will give you a temporary docker environment.

Next grab curl.

```
apt-get install curl -y
```

Then run the dependency install script:

```
bash <(curl https://raw.githubusercontent.com/ethcore/parity/master/install-deps.sh -L)
```

This will download and install all the build dependencies, in our case, that's Git, Make and GCC/G++, librocksdb, and Rust nightly.

Next, grab the parity repository:

```
git clone git@github.com:ethcore/parity && cd parity
```

You can build with:

```
cargo build
```

You can run the unit tests with:

```
cargo test --features ethcore
```

You can run the consensus tests with:

```
cargo test --features ethcore/json-tests
```

You can start a client and sync with the network with:

```
cargo run --release
```






