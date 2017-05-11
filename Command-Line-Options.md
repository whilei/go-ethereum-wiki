# Command line options

```
NAME:
   geth - the go-ethereum command line interface

USAGE:
   geth [options] command [command options] [arguments...]
VERSION:
   source
COMMANDS:
   import         import a blockchain file
   export         export blockchain into file
   upgrade-db, upgradedb      upgrade chainblock database
   remove-db, removedb        Remove blockchain and state databases
   dump           dump a specific block from storage
   monitor          Geth Monitor: node metrics monitoring and visualization
   account          manage accounts
   wallet         ethereum presale wallet
   console          Geth Console: interactive JavaScript environment
   attach         Geth Console: interactive JavaScript environment (connect to node)
   js           executes the given JavaScript files in the Geth JavaScript VM
   make-dag, makedag        generate ethash dag (for testing)
   gpu-info, gpuinfo        gpuinfo
   gpu-bench, gpubench        benchmark GPU
   version          print ethereum version numbers
   dump-chain-config, dumpchainconfig   dump current chain configuration to JSON file [REQUIRED argument: filepath.json]
   rollback, roll-back, set-head, sethead rollback [block index number] - set current head for blockchain
   help, h          Shows a list of commands or help for one command
   
ETHEREUM OPTIONS:
  --data-dir, --datadir "/Users/ia/Library/EthereumClassic" Data directory for the databases and keystore
  --chain value             Identifier of blockchain network to use (default='mainnet', test='morden').
                      Relevant data for this blockchain will correlate to subdirectories under your base data directory (--datadir), by ie $HOME/Library/EthereumClassic/mainnet/.
                      This variable can also be configured from an external JSON chain configuration file by setting the 'id' key. (default: "mainnet")
  --chain-config value, --chainconfig value     Specify a JSON format chain configuration file to use.
  --keystore              Directory for the keystore (default = inside the datadir)
  --network-id value, --networkid value       Network identifier (integer, 0=Olympic, 1=Homestead, 2=Morden) (default: 1)
  --testnet             Morden network: pre-configured test network with modified starting nonces (replay protection)
  --dev               Developer mode: pre-configured private network with several debugging flags
  --identity value            Custom node name
  --fast              Enable fast syncing through state downloads
  --light-kdf, --lightkdf         Reduce key-derivation RAM & CPU usage at some expense of KDF strength
  --cache value             Megabytes of memory allocated to internal caching (min 16MB / database forced) (default: 128)
  --blockchain-version value, --blockchainversion value   Blockchain version (integer) (default: 3)
  
ACCOUNT OPTIONS:
  --unlock value  Comma separated list of accounts to unlock
  --password value  Password file to use for non-inteactive password input
  
API AND CONSOLE OPTIONS:
  --rpc             Enable the HTTP-RPC server
  --rpc-addr value, --rpcaddr value     HTTP-RPC server listening interface (default: "localhost")
  --rpc-port value, --rpcport value     HTTP-RPC server listening port (default: 8545)
  --rpc-api value, --rpcapi value     API's offered over the HTTP-RPC interface (default: "eth,net,web3")
  --ws              Enable the WS-RPC server
  --ws-addr value, --wsaddr value     WS-RPC server listening interface (default: "localhost")
  --ws-port value, --wsport value     WS-RPC server listening port (default: 8546)
  --ws-api value, --wsapi value       API's offered over the WS-RPC interface (default: "eth,net,web3")
  --ws-origins value, --wsorigins value     Origins from which to accept websockets requests
  --ipc-disable, --ipcdisable       Disable the IPC-RPC server
  --ipc-api value, --ipcapi value     API's offered over the IPC-RPC interface (default: "admin,debug,eth,miner,net,personal,shh,txpool,web3")
  --ipc-path, --ipcpath "geth.ipc"      Filename for IPC socket/pipe within the datadir (explicit paths escape it)
  --rpc-cors-domain value, --rpccorsdomain value  Comma separated list of domains from which to accept cross origin requests (browser enforced)
  --js-path loadScript, --jspath loadScript   JavaScript root path for loadScript and document root for `admin.httpGet` (default: ".")
  --exec value            Execute JavaScript statement (only in combination with console/attach)
  --preload value         Comma separated list of JavaScript files to preload into the console
  
NETWORKING OPTIONS:
  --bootnodes value       Comma separated enode URLs for P2P discovery bootstrap
  --port value          Network listening port (default: 30303)
  --max-peers value, --maxpeers value   Maximum number of network peers (network disabled if set to 0) (default: 25)
  --max-pend-peers value, --maxpendpeers value  Maximum number of pending connection attempts (defaults used if set to 0) (default: 0)
  --nat value         NAT port mapping mechanism (any|none|upnp|pmp|extip:<IP>) (default: "any")
  --no-discover, --nodiscover     Disables the peer discovery mechanism (manual peer addition)
  --nodekey value       P2P node key file
  --nodekey-hex value, --nodekeyhex value P2P node key as hex (for testing)
  
MINER OPTIONS:
  --mine            Enable mining
  --miner-threads value, --minerthreads value   Number of CPU threads to use for mining (default: 2)
  --miner-gpus value, --minergpus value     List of GPUs to use for mining (e.g. '0,1' will use the first two GPUs found)
  --auto-dag, --autodag         Enable automatic DAG pregeneration
  --etherbase value         Public address for block mining rewards (default = first account created) (default: "0")
  --target-gas-limit value, --targetgaslimit value  Target gas limit sets the artificial target gas floor for the blocks to mine (default: "4712388")
  --gas-price value, --gasprice value     Minimal gas price to accept for mining a transactions (default: "20000000000")
  --extra-data value, --extradata value     Freeform header field set by the miner
  
GAS PRICE ORACLE OPTIONS:
  --gpo-min value, --gpomin value   Minimum suggested gas price (default: "20000000000")
  --gpo-max value, --gpomax value   Maximum suggested gas price (default: "500000000000")
  --gpo-full value, --gpofull value   Full block threshold for gas price calculation (%) (default: 80)
  --gpo-base-down value, --gpobasedown value  Suggested gas price base step down ratio (1/1000) (default: 10)
  --gpo-base-up value, --gpobaseup value  Suggested gas price base step up ratio (1/1000) (default: 100)
  --gpo-base-cf value, --gpobasecf value  Suggested gas price base correction factor (%) (default: 110)
  
LOGGING AND DEBUGGING OPTIONS:
  --verbosity value Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=core, 5=debug, 6=detail (default: 3)
  --vmodule value Per-module verbosity: comma-separated list of <pattern>=<level> (e.g. eth/*=6,p2p=5)
  --backtrace value Request a stack trace at a specific logging statement (e.g. "block.go:271") (default: :0)
  --metrics value Enables metrics reporting. When the value is a path, either relative or absolute, then a log is written to the respective file.
  --fake-pow, --fakepow Disables proof-of-work verification
  
EXPERIMENTAL OPTIONS:
  --shh   Enable Whisper
  --natspec Enable NatSpec confirmation notice
  
MISCELLANEOUS OPTIONS:
  --solc value        Solidity compiler command to be used (default: "solc")
  --doc-root, --docroot "/Users/ia" Document Root for HTTPClient file scheme
  --oppose-dao-fork     Use classic blockchain (always set, flag is unused and exists for compatibility only)
  --help, -h        show help
```

Note that the default for datadir is platform-specific. See [backup & restore](https://github.com/ethereumproject/go-ethereum/wiki/Backup-&-restore) for more information.

## Examples

### Accounts
See [Account management](https://github.com/ethereumproject/go-ethereum/wiki/Managing-your-accounts)

Import ether presale wallet into your node (prompts for password):

    geth wallet import /path/to/my/etherwallet.json

Import an EC privatekey into an ethereum account (prompts for password):

    geth account import /path/to/key.prv

### Geth JavaScript Runtime Environment 

See [Geth javascript console](https://github.com/ethereumproject/go-ethereum/wiki/JavaScript-Console)

Bring up the geth javascript console:

    geth --verbosity 5 --jspath /mydapp/js console 2>> /path/to/logfile

Execute `test.js` javascript using js API and log Debug-level messages to `/path/to/logfile`:

    geth --verbosity 6 js test.js  2>> /path/to/logfile

### Import/export chains and dump blocks

Import a blockchain from file:

    geth import blockchain.bin

### Upgrade chainblock database

When the consensus algorithm is changed blocks in the blockchain must be reimported with the new algorithm. Geth will inform the user with instructions when and how to do this when it's necessary.

    geth upgradedb

### Mining and networking

Start two mining nodes using different data directories listening on ports 30303 and 30304, respectively:

    geth --mine --minerthreads 4 --datadir /usr/local/share/ethereum/30303 --port 30303
    geth --mine --minerthreads 4 --datadir /usr/local/share/ethereum/30304 --port 30304
    
Start an rpc client on port 8000:

    geth --rpc --rpcport 8000 --rpccorsdomain "*"

Launch the client without network:

    geth --maxpeers 0 --nodiscover --networdid 3301 js justwannarunthis.js

#### Resetting the blockchain

In the datadir, delete the blockchain directory.  For an example above:

    rm -rf /usr/local/share/ethereum/30303/blockchain

### Sample usage in testing environment

The lines below are meant only for test network and safe environments for non-interactive scripted use.

```
geth --datadir /tmp/eth/42 --password <(echo -n notsosecret) account new 2>> /tmp/eth/42.log
geth --datadir /tmp/eth/42 --port 30342  js <(echo 'console.log(admin.nodeInfo().NodeUrl)') > enode 2>> /tmp/eth/42.log
geth --datadir /tmp/eth/42 --port 30342 --password <(echo -n notsosecret) --unlock primary --minerthreads 4 --mine 2>> /tmp/eth/42.log
```

### Attach
Attach a console to a running geth instance. By default this happens over IPC on the default IPC endpoint but when necessary a custom endpoint could be specified:

```
geth attach                   # connect over IPC on default endpoint
geth attach ipc:/some/path    # connect over IPC on custom endpoint
geth attach http://host:8545  # connect over HTTP
geth attach ws://host:8546    # connect over websocket
```

### External chain configuration and handling multiple chains

### --chain [kittyCoin]
Will use sub-datadir at `<home>/EthereumClassic/kittyCoin/`. This command plays nicely with `--data-dir`, too. It does _not_ play nicely with `--chain-config`. 

This flag can also be used to activate the __Morden Testnet__ configuration via `--chain=morden` or `--chain=testnet`; both flag values will assume a default sub-datadir at _/morden_. If you'd like to use a Morden testnet configuration in a subdirectory other than _/morden_, you can use `--chain mycustomtestnet --testnet` to do so.

There are a couple of chain ids that are __blacklisted__, like "nodes", "chaindata", "dapp", and so forth, because they conflict with existing directory structure namespaces.

```
$ geth --chain kittyCoin [-flags] [command]

$ geth --chain kittyCoin --data-dir path/to/etc/data [command]
$ geth --chain fakeKittyCoin --testnet [command]
```

### --chain-config [path/to/customnet.json]
Primarily for use in establishing and maintaining private blockchains and networks, configuration with an external JSON file allows fine-grained control over establishing a genesis block, implementing protocol upgrades (as fork features), and designating bootnodes.

Please find below the default Testnet configuration with comments (non-JSON-friendly).

Sets /subdir beneath parent `--data-dir`. In case of values "mainnet", "morden", and "testnet", it will _override_ the customization configuration and enable the respective defaults.
```
{
  "id": "morden", 
```

Human readable. The only optional key/value in the file.
```
  "name": "Morden Testnet", 
```

Establish a genesis block. `alloc` is optional; it establishes starting accounts and balances. Blank values here are also optional. 
```
  "genesis": {
    "nonce": "0x00006d6f7264656e",
    "timestamp": "",
    "parentHash": "",
    "extraData": "",
    "gasLimit": "0x2FEFD8",
    "difficulty": "0x020000",
    "mixhash": "0x00000000000000000000000000000000000000647572616c65787365646c6578",
    "coinbase": "",
    "alloc": {
      "0000000000000000000000000000000000000001": {
        "balance": "1"
      },
      "0000000000000000000000000000000000000002": {
        "balance": "1"
      },
      "0000000000000000000000000000000000000003": {
        "balance": "1"
      },
      "0000000000000000000000000000000000000004": {
        "balance": "1"
      },
      "102e61f5d8f9bc71d0ad4a084df4e65e05ce0e1c": {
        "balance": "1606938044258990275541962092341162602522202993782792835301376"
      }
    }
  },
```

Designate variable specifications for fork-based protocol upgrades, ie EIP/ECIPs. 
```
  "chainConfig": {
    "forks": [
      {
        "name": "Homestead",
        "block": 494000,
        "requiredHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "features": [
          {
            "id": "difficulty",
            "options": {
              "type": "homestead"
            }
          },
          {
            "id": "gastable",
            "options": {
              "type": "homestead"
            }
          }
        ]
      },
      {
        "name": "GasReprice",
        "block": 1783000,
        "requiredHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "features": [
          {
            "id": "gastable",
            "options": {
              "type": "eip150"
            }
          }
        ]
      },
      {
        "name": "The DAO Hard Fork",
        "block": 1885000,
        "requiredHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "features": []
      },
      {
        "name": "Diehard",
        "block": 1915000,
        "requiredHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "features": [
          {
            "id": "eip155",
            "options": {
              "chainID": 62
            }
          },
          {
            "id": "gastable",
            "options": {
              "type": "eip160"
            }
          },
          {
            "id": "difficulty",
            "options": {
              "length": 2000000,
              "type": "ecip1010"
            }
          }
        ]
      }
    ],
    "badHashes": [
      {
        "Block": 383792,
        "Hash": "0x9690db54968a760704d99b8118bf79d565711669cefad24b51b5b1013d827808"
      },
      {
        "Block": 1915277,
        "Hash": "0x3bef9997340acebc85b84948d849ceeff74384ddf512a20676d424e972a3c3c4"
      }
    ]
  },
```

Bootnodes for establishing a connection to a network. In `enode` format.
```
  "bootstrap": [
    "enode://fb28713820e718066a2f5df6250ae9d07cff22f672dbf26be6c75d088f821a9ad230138ba492c533a80407d054b1436ef18e951bb65e6901553516c8dffe8ff0@104.155.176.151:30304",
    "enode://afdc6076b9bf3e7d3d01442d6841071e84c76c73a7016cb4f35c0437df219db38565766234448f1592a07ba5295a867f0ce87b359bf50311ed0b830a2361392d@104.154.136.117:30403",
    "enode://21101a9597b79e933e17bc94ef3506fe99a137808907aa8fefa67eea4b789792ad11fb391f38b00087f8800a2d3dff011572b62a31232133dd1591ac2d1502c8@104.198.71.200:30403",
    "enode://fd008499e9c4662f384b3cff23438879d31ced24e2d19504c6389bc6da6c882f9c2f8dbed972f7058d7650337f54e4ba17bb49c7d11882dd1731d26a6e62e3cb@35.187.57.94:30304"
  ]
}
```


#### dump-chain-config [path/to/dump_customnet.json]
```
$ geth [-flags] dump-external-config put/it/here/customnet.json

$ geth --chain kittyCoin dump-external-config put/it/here/customnet.json
$ geth --data-dir my/etc/data dump-external-config put/it/here/customnet.json
$ geth --chain=morden dump-external-config put/it/here/customnet.json
$ geth --bootnodes=enode://asdfasdf@12.123.12.12:12345 dump-external-config put/it/here/customnet.json
```

### rollback [42]

Where 42 is the _block number_ to rollback to. Uses `blockchain.SetHead()` method. __This is a destructive action!__ It will _purge block data_ antecedent to the provided block; syncing will begin with your new head block. Probably mostly useful for development and testing.







