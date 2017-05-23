# 3.4.0

### New features

- `rollback` set head and purge blocks antecedent to specified block number.
- `--chain-config` replaces `init [genesis]` , allowing finer-grained control over a private network configuration.
- `dump-chain-config` is useful for establishing starting-point `customnet.json` external chain configurations.
- `--chain` allows you to specify chain to run by "chainID", ie "mainnet", "morden", and "customnet". Its value selects the _parent/chain_ subdirectory in which to store all chain and node data.

To learn more about these new features, check out the [Command Line Options wiki page](https://github.com/ethereumproject/go-ethereum/wiki/Command-Line-Options) near the bottom.


### Data directory migration

We've renamed the base default data directory from "Ethereum" to "EthereumClassic" (or OS-sensible variants). Along with this and the implementation of `--chain` flag, chain-based data are now stored by named _subdirectories_ under the parent data dir. This means

out with the **old**:

```bash
# morden testnet defaults
.../Ethereum/testnet/chaindata
.../Ethereum/testnet/nodes
.../Ethereum/testnet/keystore

# mainnet defaults
.../Ethereum/chaindata 
.../Ethereum/nodes
.../Ethereum/keystore
```
in with the **new**: 
```bash
# morden testnet defaults
.../EthereumClassic/morden/chaindata
.../EthereumClassic/morden/nodes
.../EthereumClassic/morden/keystore

# mainnet defaults
.../EthereumClassic/mainnet/chaindata
.../EthereumClassic/mainnet/nodes
.../EthereumClassic/mainnet/keystore
```

Geth will attempt to migrate (`mv`) any relevant data from the old schema to the new, __unless__:
 - it can't find pertinent _ETC_ data in the old directories, 
 - the "old" data isn't in the _default locations_, 
 - or you _override_ the defaults with the `--data-dir`, `--chain=customnet` or `--external-config` flags,
 - it already has, in which case you're up-to-date


### :bug:'s squished and other improvements
- All commands and flags which are conjuctions (like `--datadir`) have been aliased with their `--data-dir` hyphen-case pair.
- Attempts to use an invalid command will show help instead of being ignored.
- Public API `GetBlockByNumber` now populates `logsBloom` field (thanks @tranvictor).
- Logging _sync busy_ has been relegated to `Debug` level.
- Improved tests, their coverage, and OS-agnosticism.


