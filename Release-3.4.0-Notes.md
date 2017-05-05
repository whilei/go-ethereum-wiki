# 3.4.0

### New features

- `rollback` set head and purge blocks antecedent to specified block number
- `--chain-config` replaces `init` genesis, allowing ever finer-grained control over a private network configuration
- `dump-chain-config` is useful for establishing starting-point `customnet.json` external chain configurations
- `--chain` allows you to specify chain to run by "chainID", ie "mainnet", "customnet"...


### Data directory migration

We've renamed the base default data directory from "Ethereum" to "EthereumClassic" (or OS-sensible variants). Along with this and the implementation of `--chain` flag, chain-based data are now store by named _subdirectories_ under the parent data dir. This means

```
.../Ethereum/testnet/chaindata
.../Ethereum/testnet/nodes
.../Ethereum/testnet/keystore
.../Ethereum/chaindata # mainnet defaults
.../Ethereum/nodes
.../Ethereum/keystore
```
becomes: 
```
.../EthereumClassic/morden/chaindata
.../EthereumClassic/morden/nodes
.../EthereumClassic/morden/keystore
.../EthereumClassic/mainnet/chaindata # mainnet defaults
.../EthereumClassic/mainnet/nodes
.../EthereumClassic/mainnet/keystore
```

Geth will attempt to migrate (rename,`mv`) any relevant data from the old schema to the new, __unless__:
 - it can't find _ETC (vs ETH)_ data in the old directories, 
 - the "old" data isn't in the _default locations_, 
 - or you _override_ the in-use defaults with the `--data-dir`, `--chain`, or `--external-config` flags.



