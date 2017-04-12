## Building from source

### Building geth (command line client)

Clone the repository to a directory of your choosing:

```shell
git clone https://github.com/ethereumproject/go-ethereum
```

Building `geth` requires some external libraries to be installed:

* [GMP](https://gmplib.org)
* [Go](https://golang.org)

```shell
$ brew install gmp go
```

It's recommended to use the latest version of Go (>=1.8). To upgrade Go with Homebrew use `$ brew upgrade go`, though be advised an upgrade may have implications for software outside of Ethereum Classic.

Finally, build the `geth` program using the following command.
```shell
$ cd go-ethereum
$ go install cmd/geth # installs at $GOPATH/bin/geth
$ go install cmd/... # installs all command executables under `/cmd/`
```

You can now run `source $GOPATH/bin/geth` to start your node. __Protip:__ If your `$GOPATH/bin` is in your environment's `$PATH`, you'll be able to simply run `$ geth`.

Run `$ geth --help` for an overview of options. To set up a new account, check out the [Managing Accounts page](https://github.com/ethereumproject/go-ethereum/wiki/Managing-your-accounts).