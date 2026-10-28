# ccxt-go (trimmed)

[CCXT](https://github.com/ccxt/ccxt)'s Go package, trimmed to the exchanges
[tfc](https://github.com/tfc11001/tfc) uses. Nothing here is our code: it is
CCXT's own source (MIT, see `LICENSE`), with every other exchange's files
deleted.

**Why.** CCXT's Go package holds every exchange (100+) in one Go package, and
Go compiles a package whole: about 14.6 GB of RAM and 40 s to build, on every
CI job and image build. Trimmed to three exchanges it takes about 1.4 GB.

## What the trim changes

`trim.sh` takes CCXT's Go module at a release and:

1. copies its non-test `.go` files, `protoc/`, `LICENSE` and `go.sum`;
2. deletes each exchange not kept (`<id>.go`, `<id>_api.go`, `<id>_wrapper.go`);
3. removes those exchanges' cases from the two by-name registries
   (`exchange_dynamic.go`, `exchange_typed_interface.go`);
Nothing else is edited, `go.mod` included: the module keeps CCXT's own path,
`github.com/ccxt/ccxt/go/v4`, as forks used through `replace` do. `UPSTREAM_VERSION` and `EXCHANGES` record what the
current tree was built from.

## Using it

The API is CCXT's, so code imports `github.com/ccxt/ccxt/go/v4` as usual and
`go.mod` points that path here:

```
require github.com/ccxt/ccxt/go/v4 v4.5.77
replace github.com/ccxt/ccxt/go/v4 => github.com/tfc11001/ccxt-go/v4 v4.5.77-tfc.2
```

## Adding an exchange or upgrading CCXT

```
./trim.sh v4.5.77 binance kraken coinbase kucoin   # add an exchange
./trim.sh v4.6.0 binance kraken coinbase           # upgrade
```

Commit the result and tag it `<upstream version>-tfc.<n>` (e.g.
`v4.5.77-tfc.3`), then bump the `replace` line in tfc.
