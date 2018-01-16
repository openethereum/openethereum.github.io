The first experimental light client was released with 1.7. To try out the light mode, simply run `parity --light`.

    --light                        Experimental: run in light client mode. Light clients
                                   synchronize a bare minimum of data and fetch necessary
                                   data on-demand from the network. Much lower in storage,
                                   potentially higher in bandwidth. Has no effect with
                                   subcommands (default: false).

By default, each Parity node serves state data for connected light peers. To disable this, use the `--no-serve-light` command-line flag.

    --no-serve-light               Disable serving of light peers. (default: false)
 
Read more on the [Parity Light Protocol](The-Parity-Light-Protocol-(PIP).md) and check the [road map](https://github.com/paritytech/parity/projects/2) on GitHub.
