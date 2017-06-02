# 3.5.0

### Changes

#### Added
- _Option_: `--index-accounts` - use persistent keystore key file indexing (recommended for use with greater than ~10k-100k+ key files)
- _Command_: `--index-accounts account index` - build or rebuild persisent key file index
- _Option_: `--log-dir` - specify directory in which to redirect logs to files
- Created CHANGELOG.md

#### Changed
- _Command_: `dump <blockHash|blockNum>,<|blockHash|blockNum> <|address>,<|address>` - specify dump for _n_ block(s) for optionally _a_ address(es)
- _Option__: `--chain` replaces `--chain-config` and expects consistent custom external chain config JSON path (`datadir/custom/chain.json`)

#### Fixed
- SIGSEGV crash on malformed ChainID signer for replay-protected blocks.
- Hash map exploit opportunity (thanks @karalabe)

#### Removed
- _Option_: `--chain-config`, replaced by `--chain`
