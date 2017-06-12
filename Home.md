<p align="center">
  <img src="./wiki/images/ETC_LOGO_Full_Color_Black.png" width="150"/>
</p>

__Go-Ethereum__ is a Go language implementation of Ethereum Classic, supporting the _original_ blockchain and its philosophy of immutability,  censorship-resistance, and resilient distributed applications. 

:telescope: For __general information__ related to Ethereum Classic including:

- whitepaper 
- yellow paper 
- protocol and interface specs
- affiliated APIs 
- DAPP development guides

... please see our [Ethereum Project Main Wiki](https://github.com/ethereumproject/wiki/wiki) or visit [our website](https://ethereumclassic.github.io). 

### Geth
The main Ethereum Classic client is called Geth (the old english third person singular conjugation of "to go". Quite appropriate given geth is written in [Go](https://golang.org/). Geth is a multipurpose command line tool that runs a full Ethereum Classic node. It offers three interfaces: 

- the [command line subcommands and options](./Command-Line-Options), 
- a [JSON-RPC](https://github.com/ethereumproject/wiki/wiki/JSON-RPC) server, and
- an interactive [Javascript console](./JavaScript-Console). 

__Platforms:__ Supported Platforms are Linux, Mac, and Windows. In order to install Geth, please vist our [Releases Page](github.com/ethereumproject/go-ethereum/releases). Please consider reviewing our [Disclaimer Notice](./Disclaimer) before use.

__License:__ The Ethereum Classic Core Protocol licensed under the [GNU Lesser General Public License 3.0](https://www.gnu.org/licenses/lgpl.html). All frontend client software (under [cmd](https://github.com/ethereumproject/go-ethereum/tree/master/cmd)) is licensed under the [GNU General Public License](https://www.gnu.org/copyleft/gpl.html).

By installing and running `geth`, you can take part in the Ethereum Classic live network and
- mine real ether 
- transfer funds between addresses
- create contracts and send transactions
- explore block history
- and much, much more

#### Basic Use Case Documentation
- [Managing Accounts](./Managing-Accounts)
- [Mining](./mining)
- [Contracts and Transactions](./Contracts-and-Transactions)

### Developers
This is an open source project made by and for a passionate and [welcoming community](https://github.com/ethereumproject/volunteer). The ETCDEV team is lead by @splix, and __Go-Ethereum__'s lead developer is @whilei (who goes by @ia on Slack). Get yourself [an invitation to Slack](http://ethereumclassic.herokuapp.com/), read the [Contributing Guidelines](https://github.com/ethereumproject/rfc/blob/master/1/README.md), and [find or make an issue](https://github.com/ethereumproject/go-ethereum/issues).

#### Install dependencies and build
Building and testing geth requires both Go >=1.8 and a C compiler.

> [Installing Go Wiki page](./Installing-Go)

Get set up:
```bash
# Go get it! (get it? ;-)
go get github.com/ethereumproject/go-ethereum/...

# Install binary 'geth' to $GOPATH/bin:
# Note: You can run this command from $cwd.
# Note: Ensure $GOPATH/bin is added to your $PATH.
go install github.com/ethereumproject/go-ethereum/cmd/geth
# Or, install all executables, including geth:
go install github.com/ethereumproject/go-ethereum/cmd/...

# check it out!
geth --help
```

#### Testing
See the [Testing Wiki page](./Testing) for information on unit, integration, and external tests. 

#### Logging
Geth outputs `stderr` to the console. Output from the console can be logged or redirected:
```
geth 2>>geth.log
```

Additionally, you may can use `--log-dir=PATH` to specify a directly in which geth will write it's logs to a timestamped file.

You can also use `geth attach` to begin a Javascript console session with an already-running instance of geth; just use a second terminal.

#### Further
- If you're integrating another application or service with geth:
    + [Management API](./Management-APIs)
    + [JSON-RPC](https://github.com/ethereumproject/wiki/wiki/JSON-RPC)
    + [Contracts and Transactions](https://github.com/ethereumproject/wiki/wiki/Contracts-and-Transactions)
- Interested in working with some associated packages?
    + [P2P](./Peer-To-Peer)    

### Issues and Support

Please browse our [FAQ Wiki page](./FAQ) to see if there's already an answer to your question. If there isn't, please file an issue or get [in touch with us on Slack](http://ethereumclassic.herokuapp.com/) (#help or #development channels, preferably).

#### Reporting 

Security issues are best sent to splix@etcdevteam.org, isaac.ardis@gmail.com, or shared in PM with devs on one of the Slack channels.

Non-sensitive bug reports are welcome on Github. Please always state the version (on master) or commit of your build (if on develop), give as much detail as possible about the situation and the anomaly that occurred. Provide logs or stacktrace if you can.

### Contributors

Ethereum is joint work of ETCDEV and the community.

Name or blame = list of contributors:

- [go-ethereum](https://github.com/ethereumproject/go-ethereum/graphs/contributors)
- [cpp-ethereum](https://github.com/ethereumproject/cpp-ethereum/graphs/contributors)
- [web3.js](https://github.com/ethereumproject/web3.js/graphs/contributors)
- [ethash](https://github.com/ethereumproject/ethash/graphs/contributors)
- [netstats](https://github.com/cubedro/eth-netstats/graphs/contributors), 
[netintelligence-api](https://github.com/cubedro/eth-net-intelligence-api/graphs/contributors)



