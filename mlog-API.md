# mlog API

* _Note_: this feature is forthcoming, and hasn't yet been included in a release.

----

`mlog` stands for "machine logging." To read the spec for mlog implementation
and documenation, please visit the [mlog spec](./mlog-Spec) page.

mlog has the following option flags:

- `--mlog` (default:`kv`): specifies format in which mlog lines should be structured; possible format values are `kv` (key-value), `json`, and `plain` (same as key-value, but with just values), as well as `off` to disable mlog entirely
- `--mlog-dir` (default: `<datadir>/<chain-identity>/mlogs`): directory to hold mlogs
- `--mlog-components` (default: all available): comma-separated list of components which should be logged. For a list of available components, please see the [lines reference section](#lines-reference).

Machine logging is enabled by default. To disable it, use `--mlog=off`.


#### Directory and files

Machine log files are automatically generated in the `/mlogs` subdirectory of the chain data directory of the running geth instance, for example:

```
$HOME/Library/EthereumClassic/mainnet/mlogs/
```

To customize this location, use `--mlog-dir=/path/to/mlogs`, where `/path/to/mlogs` may be an absolute or relative path to a directory. This directory will be created if it doesn't exist.

Machine log file names include context and time information. The latest
mlog file will be symlinked as `<programname>.log`. For example:

```
lrwxr-xr-x 1 ia staff   37 Aug  1 13:49 geth.log -> geth.mh.ia.mlog.20170801-134958.33641
-rw-r--r-- 1 ia staff    0 Aug  1 13:45 geth.mh.ia.mlog.20170801-134515.32147
-rw-r--r-- 1 ia staff 6.0K Aug  1 13:49 geth.mh.ia.mlog.20170801-134853.33260
-rw-r--r-- 1 ia staff 3.8K Aug  1 13:49 geth.mh.ia.mlog.20170801-134946.33525
-rw-r--r-- 1 ia staff    0 Aug  1 13:49 geth.mh.ia.mlog.20170801-134958.33641
```

A new mlog file is generated each time a geth node is run.

#### Line structure

A line is the result of a single event in the go-ethereum protocol. For brevity, the following structure documentation refers to `plain` and `kv`-formatted lines. For notes on `json`-formatting, please refer to the section below.

Lines are organized with a common structure:

```
$DATE $TIME [$cmp] RECEIVER VERB SUBJECT [$RECEIVER:DETAIL] [$RECEIVER:DETAIL] [$SUBJECT:DETAIL] [$SUBJECT:DETAIL]
```

where the number and types of details vary for different lines.

For example:
```
2017/08/01 13:50:23 [discover] PING HANDLE FROM [123.45.67.89:30303] [7909d51011d8a153] [252]
```

Line structure can be understood in three parts:

__Header__: `2017/08/01 13:50:23 [discover]`

The first value is the __date__, of the form `%Y/%m/%d`. The second value is the __time__, of the form `%H:%M:%S`.
The third value is the trace __component__ calling the log. The component name will always be in brackets. Header structure is the same for all `kv` and `plain` lines, but is excluded from `json`-formatted lines.

__Fingerprint__: `PING HANDLE FROM`

lines are referred to and organized by their unique fingerprint pattern: `RECEIVER VERB SUBJECT`.
For `json`-formatted lines, this pattern will be present within the logged json objects as
`"event": "receiver.subject.verb"`.


__Details__: `[123.45.67.89:30303] [7909d51011d8a153] [252]`

All DETAIL values are wrapped in brackets to improve ease of parsing, especially when the value is a string that may contain spaces, eg. `[empty header received]`.

If formatting is set to __key-value__, DETAILS will include `owner.key=[$SOMEVALUE]`, eg:

```
from.udp_address=[123.45.67.89:30303] from.id=[7909d51011d8a153] ping.bytes_transferred=[252]
```

_Note_: In this documentation, line variable names which represent dynamic values (eg. `$STRING`)
are prefixed with `$`, while _constant_ values (eg. `SEND`) are _not_ prefixed.


#### JSON-formatted line structure

Each line logged in JSON format contains a single JSON object, and _is not_ prefixed by a timestamp or component prefix.

__Additional keys__

Each JSON-formatted line contains two additional keys:

- `"event"`: the 'fingerprint' of the line event. This, for example, may be used as the `json.event_key` value for Logstash.
- `"ts"`: the timestamp of the event. Note that this is the time at which the event was logged,
not the time at which the line may be ingested by a log parsing program. The value will be in the form `2017-08-10T15:20:11.294317237-05:00`.


__Flatness__

The JSON line details should be as "flat" as possible; instead of presenting:

```json
{"node": {"ip": "1234asdf", "port": 3333}}
```

the line will prefer a format like:

```json
{"node.ip": "1234asdf", "node.port": 3333}
```

# Lines reference

| Component | Description |
| --- | --- |
| discover | The discover component handles low-level peer discovery requests. |
| blockchain | The blockchain component tracks block and chain insertions into the chaindb during import and sync. |
| server | The server component tracks peer-to-peer registrations. |
| miner | The miner component tracks mining actions, including uncle and transaction commits. |
| state | The state component tracks address creation and balance changes. |
| txpool | The txpool component tracks the addition and validation of transactions. |


[txpool]

- [txpool.add.tx](#txpool-add-tx)
- [txpool.validate.tx](#txpool-validate-tx)

[miner]

- [miner.start.coinbase](#miner-start-coinbase)
- [miner.stop.coinbase](#miner-stop-coinbase)
- [miner.commit_work.block](#miner-commit_work-block)
- [miner.commit.uncle](#miner-commit-uncle)
- [miner.commit.tx](#miner-commit-tx)
- [miner.mine.block](#miner-mine-block)
- [miner.confirm_mined.block](#miner-confirm_mined-block)

[server]

- [server.add.peer](#server-add-peer)
- [server.remove.peer](#server-remove-peer)

[discover]

- [ping.handle.from](#ping-handle-from)
- [ping.send.to](#ping-send-to)
- [pong.handle.from](#pong-handle-from)
- [pong.send.to](#pong-send-to)
- [findnode.handle.from](#findnode-handle-from)
- [findnode.send.to](#findnode-send-to)
- [neighbors.handle.from](#neighbors-handle-from)
- [neighbors.send.to](#neighbors-send-to)

[state]

- [state.create.object](#state-create-object)
- [state.add_balance.object](#state-add_balance-object)
- [state.sub_balance.object](#state-sub_balance-object)

[blockchain]

- [blockchain.write.block](#blockchain-write-block)
- [blockchain.insert.blocks](#blockchain-insert-blocks)

----


#### PING HANDLE FROM
Called once for each received PING request from peer FROM.

__Key value__:
```
2017/08/15 14:50:23 [discover] PING HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] ping.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"ping.handle.from","from.id":"string","from.udp_address":"string","ping.bytes_transferred":0,"ts":"2017-08-15T14:50:23.661532837-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] PING HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `ping.bytes_transferred`: $INT



#### PING SEND TO
Called once for each outgoing PING request to peer TO.

__Key value__:
```
2017/08/15 14:50:23 [discover] PING SEND TO to.udp_address=[$STRING] ping.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"ping.send.to","ping.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-15T14:50:23.661989357-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] PING SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `ping.bytes_transferred`: $INT



#### PONG HANDLE FROM
Called once for each received PONG request from peer FROM.

__Key value__:
```
2017/08/15 14:50:23 [discover] PONG HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] pong.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"pong.handle.from","from.id":"string","from.udp_address":"string","pong.bytes_transferred":0,"ts":"2017-08-15T14:50:23.662038737-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] PONG HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `pong.bytes_transferred`: $INT



#### PONG SEND TO
Called once for each outgoing PONG request to peer TO.

__Key value__:
```
2017/08/15 14:50:23 [discover] PONG SEND TO to.udp_address=[$STRING] pong.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"pong.send.to","pong.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-15T14:50:23.662131431-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] PONG SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `pong.bytes_transferred`: $INT



#### FINDNODE HANDLE FROM
Called once for each received FIND_NODE request from peer FROM.

__Key value__:
```
2017/08/15 14:50:23 [discover] FINDNODE HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] findnode.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"findnode.handle.from","findnode.bytes_transferred":0,"from.id":"string","from.udp_address":"string","ts":"2017-08-15T14:50:23.662175285-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] FINDNODE HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `findnode.bytes_transferred`: $INT



#### FINDNODE SEND TO
Called once for each received FIND_NODE request from peer FROM.

__Key value__:
```
2017/08/15 14:50:23 [discover] FINDNODE SEND TO to.udp_address=[$STRING] findnode.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"findnode.send.to","findnode.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-15T14:50:23.66221834-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] FINDNODE SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `findnode.bytes_transferred`: $INT



#### NEIGHBORS HANDLE FROM
Called once for each received NEIGHBORS request from peer FROM.

__Key value__:
```
2017/08/15 14:50:23 [discover] NEIGHBORS HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] neighbors.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"neighbors.handle.from","from.id":"string","from.udp_address":"string","neighbors.bytes_transferred":0,"ts":"2017-08-15T14:50:23.662256722-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] NEIGHBORS HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `neighbors.bytes_transferred`: $INT



#### NEIGHBORS SEND TO
Called once for each outgoing NEIGHBORS request to peer TO.

__Key value__:
```
2017/08/15 14:50:23 [discover] NEIGHBORS SEND TO to.udp_address=[$STRING] neighbors.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"neighbors.send.to","neighbors.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-15T14:50:23.662311152-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [discover] NEIGHBORS SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `neighbors.bytes_transferred`: $INT



#### STATE CREATE OBJECT
Called once when a state object is created.
$OBJECT.NEW is the address of the newly-created object.
If there was an existing account with the same address, it is overwritten and its address returned as the $OBJECT.PREV value.

__Key value__:
```
2017/08/15 14:50:23 [state] STATE CREATE OBJECT object.new=[$STRING] object.prev=[$STRING]
```

__JSON__:
```json
{"event":"state.create.object","object.new":"string","object.prev":"string","ts":"2017-08-15T14:50:23.6623435-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [state] STATE CREATE OBJECT [$STRING] [$STRING]
```

_2 detail values_:

- `object.new`: $STRING
- `object.prev`: $STRING



#### STATE ADD_BALANCE OBJECT
Called once when the balance of an account (state object) is added to.

__Key value__:
```
2017/08/15 14:50:23 [state] STATE ADD_BALANCE OBJECT object.address=[$STRING] object.nonce=[$INT] object.balance=[$BIGINT] add_balance.amount=[$BIGINT]
```

__JSON__:
```json
{"add_balance.amount":0,"event":"state.add_balance.object","object.address":"string","object.balance":0,"object.nonce":0,"ts":"2017-08-15T14:50:23.662378004-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [state] STATE ADD_BALANCE OBJECT [$STRING] [$INT] [$BIGINT] [$BIGINT]
```

_4 detail values_:

- `object.address`: $STRING
- `object.nonce`: $INT
- `object.balance`: $BIGINT
- `add_balance.amount`: $BIGINT



#### STATE SUB_BALANCE OBJECT
Called once when the balance of an account (state object) is subtracted from.

__Key value__:
```
2017/08/15 14:50:23 [state] STATE SUB_BALANCE OBJECT object.address=[$STRING] object.nonce=[$INT] object.balance=[$BIGINT] sub_balance.amount=[$BIGINT]
```

__JSON__:
```json
{"event":"state.sub_balance.object","object.address":"string","object.balance":0,"object.nonce":0,"sub_balance.amount":0,"ts":"2017-08-15T14:50:23.662459252-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [state] STATE SUB_BALANCE OBJECT [$STRING] [$INT] [$BIGINT] [$BIGINT]
```

_4 detail values_:

- `object.address`: $STRING
- `object.nonce`: $INT
- `object.balance`: $BIGINT
- `sub_balance.amount`: $BIGINT



#### BLOCKCHAIN WRITE BLOCK
Called when a single block written to the chain database.
A STATUS of NONE means it was written _without_ any abnormal chain event, such as a split.

__Key value__:
```
2017/08/15 14:50:23 [blockchain] BLOCKCHAIN WRITE BLOCK write.status=[$STRING] write.error=[$STRING_OR_NULL] block.number=[$BIGINT] block.hash=[$STRING] block.size=[$INT64] block.transactions_count=[$INT] block.gas_used=[$BIGINT] block.coinbase=[$STRING] block.time=[$BIGINT]
```

__JSON__:
```json
{"block.coinbase":"string","block.gas_used":0,"block.hash":"string","block.number":0,"block.size":null,"block.time":0,"block.transactions_count":0,"event":"blockchain.write.block","ts":"2017-08-15T14:50:23.662516184-05:00","write.error":null,"write.status":"string"}
```

__Plain__:
```
2017/08/15 14:50:23 [blockchain] BLOCKCHAIN WRITE BLOCK [$STRING] [$STRING_OR_NULL] [$BIGINT] [$STRING] [$INT64] [$INT] [$BIGINT] [$STRING] [$BIGINT]
```

_9 detail values_:

- `write.status`: $STRING
- `write.error`: $STRING_OR_NULL
- `block.number`: $BIGINT
- `block.hash`: $STRING
- `block.size`: $INT64
- `block.transactions_count`: $INT
- `block.gas_used`: $BIGINT
- `block.coinbase`: $STRING
- `block.time`: $BIGINT



#### BLOCKCHAIN INSERT BLOCKS
Called when a chain of blocks is inserted into the chain database.

__Key value__:
```
2017/08/15 14:50:23 [blockchain] BLOCKCHAIN INSERT BLOCKS blocks.count=[$INT] blocks.queued=[$INT] blocks.ignored=[$INT] blocks.transactions_count=[$INT] blocks.last_number=[$BIGINT] blocks.first_hash=[$STRING] blocks.last_hash=[$STRING] insert.time=[$DURATION]
```

__JSON__:
```json
{"blocks.count":0,"blocks.first_hash":"string","blocks.ignored":0,"blocks.last_hash":"string","blocks.last_number":0,"blocks.queued":0,"blocks.transactions_count":0,"event":"blockchain.insert.blocks","insert.time":63042000000,"ts":"2017-08-15T14:50:23.662601986-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [blockchain] BLOCKCHAIN INSERT BLOCKS [$INT] [$INT] [$INT] [$INT] [$BIGINT] [$STRING] [$STRING] [$DURATION]
```

_8 detail values_:

- `blocks.count`: $INT
- `blocks.queued`: $INT
- `blocks.ignored`: $INT
- `blocks.transactions_count`: $INT
- `blocks.last_number`: $BIGINT
- `blocks.first_hash`: $STRING
- `blocks.last_hash`: $STRING
- `insert.time`: $DURATION



#### TXPOOL ADD TX
Called once when a valid transaction is added to tx pool.
$TO.NAME will be the account address hex or '[NEW_CONTRACT]' in case of a contract.

__Key value__:
```
2017/08/15 14:50:23 [txpool] TXPOOL ADD TX tx.from=[$STRING] tx.to=[$STRING] tx.value=[$BIGINT] tx.hash=[$STRING]
```

__JSON__:
```json
{"event":"txpool.add.tx","ts":"2017-08-15T14:50:23.662687961-05:00","tx.from":"string","tx.hash":"string","tx.to":"string","tx.value":0}
```

__Plain__:
```
2017/08/15 14:50:23 [txpool] TXPOOL ADD TX [$STRING] [$STRING] [$BIGINT] [$STRING]
```

_4 detail values_:

- `tx.from`: $STRING
- `tx.to`: $STRING
- `tx.value`: $BIGINT
- `tx.hash`: $STRING



#### TXPOOL VALIDATE TX
Called once when validating a single transaction.
If transaction is invalid, TX.ERROR will be non-nil, otherwise it will be nil.

__Key value__:
```
2017/08/15 14:50:23 [txpool] TXPOOL VALIDATE TX tx.hash=[$STRING] tx.error=[$STRING_OR_NULL]
```

__JSON__:
```json
{"event":"txpool.validate.tx","ts":"2017-08-15T14:50:23.662734219-05:00","tx.error":null,"tx.hash":"string"}
```

__Plain__:
```
2017/08/15 14:50:23 [txpool] TXPOOL VALIDATE TX [$STRING] [$STRING_OR_NULL]
```

_2 detail values_:

- `tx.hash`: $STRING
- `tx.error`: $STRING_OR_NULL



#### MINER START COINBASE
Called once when the miner starts.

__Key value__:
```
2017/08/15 14:50:23 [miner] MINER START COINBASE coinbase.address=[$STRING] miner.threads=[$INT]
```

__JSON__:
```json
{"coinbase.address":"string","event":"miner.start.coinbase","miner.threads":0,"ts":"2017-08-15T14:50:23.662776325-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [miner] MINER START COINBASE [$STRING] [$INT]
```

_2 detail values_:

- `coinbase.address`: $STRING
- `miner.threads`: $INT



#### MINER STOP COINBASE
Called once when the miner stops.

__Key value__:
```
2017/08/15 14:50:23 [miner] MINER STOP COINBASE coinbase.address=[$STRING] miner.threads=[$INT]
```

__JSON__:
```json
{"coinbase.address":"string","event":"miner.stop.coinbase","miner.threads":0,"ts":"2017-08-15T14:50:23.662818841-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [miner] MINER STOP COINBASE [$STRING] [$INT]
```

_2 detail values_:

- `coinbase.address`: $STRING
- `miner.threads`: $INT



#### MINER COMMIT_WORK BLOCK
Called when the miner commits new work on a block.

__Key value__:
```
2017/08/15 14:50:23 [miner] MINER COMMIT_WORK BLOCK block.number=[$BIGINT] commit_work.txs_count=[$INT] commit_work.uncles_count=[$INT] commit_work.time_elapsed=[$DURATION]
```

__JSON__:
```json
{"block.number":0,"commit_work.time_elapsed":63042000000,"commit_work.txs_count":0,"commit_work.uncles_count":0,"event":"miner.commit_work.block","ts":"2017-08-15T14:50:23.66284906-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [miner] MINER COMMIT_WORK BLOCK [$BIGINT] [$INT] [$INT] [$DURATION]
```

_4 detail values_:

- `block.number`: $BIGINT
- `commit_work.txs_count`: $INT
- `commit_work.uncles_count`: $INT
- `commit_work.time_elapsed`: $DURATION



#### MINER COMMIT UNClE
Called when the miner commits an uncle.
If $COMMIT_UNCLE is non-nil, uncle is not committed.

__Key value__:
```
2017/08/15 14:50:23 [miner] MINER COMMIT UNClE commit.block_number=[$BIGINT] uncle.hash=[$STRING] commit.error=[$STRING_OR_NULL]
```

__JSON__:
```json
{"commit.block_number":0,"commit.error":null,"event":"miner.commit.uncle","ts":"2017-08-15T14:50:23.662904997-05:00","uncle.hash":"string"}
```

__Plain__:
```
2017/08/15 14:50:23 [miner] MINER COMMIT UNClE [$BIGINT] [$STRING] [$STRING_OR_NULL]
```

_3 detail values_:

- `commit.block_number`: $BIGINT
- `uncle.hash`: $STRING
- `commit.error`: $STRING_OR_NULL



#### MINER COMMIT TX
Called when the miner commits (or attempts to commit) a transaction.
If $COMMIT.ERROR is non-nil, the transaction is not committed.

__Key value__:
```
2017/08/15 14:50:23 [miner] MINER COMMIT TX commit.block_number=[$BIGINT] tx.hash=[$STRING] commit.error=[$STRING]
```

__JSON__:
```json
{"commit.block_number":0,"commit.error":"string","event":"miner.commit.tx","ts":"2017-08-15T14:50:23.662951614-05:00","tx.hash":"string"}
```

__Plain__:
```
2017/08/15 14:50:23 [miner] MINER COMMIT TX [$BIGINT] [$STRING] [$STRING]
```

_3 detail values_:

- `commit.block_number`: $BIGINT
- `tx.hash`: $STRING
- `commit.error`: $STRING



#### MINER MINE BLOCK
Called when the miner has mined a block.
$BLOCK.STATUS will be either 'stale' or 'wait_confirm'. $BLOCK.WAIT_CONFIRM is an integer
specifying _n_ blocks to wait for confirmation based on block depth, relevant only for when $BLOCK.STATUS is 'wait_confirm'.

__Key value__:
```
2017/08/15 14:50:23 [miner] MINER MINE BLOCK block.number=[$BIGINT] block.hash=[$STRING] block.status=[$STRING] block.wait_confirm=[$INT]
```

__JSON__:
```json
{"block.hash":"string","block.number":0,"block.status":"string","block.wait_confirm":0,"event":"miner.mine.block","ts":"2017-08-15T14:50:23.663016133-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [miner] MINER MINE BLOCK [$BIGINT] [$STRING] [$STRING] [$INT]
```

_4 detail values_:

- `block.number`: $BIGINT
- `block.hash`: $STRING
- `block.status`: $STRING
- `block.wait_confirm`: $INT



#### MINER CONFIRM_MINED BLOCK
Called once to confirm the miner has mined a block _n_ blocks back,
where _n_ refers to the $BLOCK.WAIT_CONFIRM value from MINER MINE BLOCK.
This is only a logging confirmation message, and is not related to work done.

__Key value__:
```
2017/08/15 14:50:23 [miner] MINER CONFIRM_MINED BLOCK block.number=[$INT]
```

__JSON__:
```json
{"block.number":0,"event":"miner.confirm_mined.block","ts":"2017-08-15T14:50:23.663066974-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [miner] MINER CONFIRM_MINED BLOCK [$INT]
```

_1 detail values_:

- `block.number`: $INT



#### SERVER ADD PEER
Called once when a peer is added.

__Key value__:
```
2017/08/15 14:50:23 [server] SERVER ADD PEER server.peer_count=[$INT] peer.id=[$STRING] peer.remote_addr=[$STRING] peer.remote_version=[$STRING]
```

__JSON__:
```json
{"event":"server.add.peer","peer.id":"string","peer.remote_addr":"string","peer.remote_version":"string","server.peer_count":0,"ts":"2017-08-15T14:50:23.663108066-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [server] SERVER ADD PEER [$INT] [$STRING] [$STRING] [$STRING]
```

_4 detail values_:

- `server.peer_count`: $INT
- `peer.id`: $STRING
- `peer.remote_addr`: $STRING
- `peer.remote_version`: $STRING



#### SERVER REMOVE PEER
Called once when a peer is removed.

__Key value__:
```
2017/08/15 14:50:23 [server] SERVER REMOVE PEER server.peer_count=[$INT] peer.id=[$STRING] remove.reason=[$STRING]
```

__JSON__:
```json
{"event":"server.remove.peer","peer.id":"string","remove.reason":"string","server.peer_count":0,"ts":"2017-08-15T14:50:23.663165277-05:00"}
```

__Plain__:
```
2017/08/15 14:50:23 [server] SERVER REMOVE PEER [$INT] [$STRING] [$STRING]
```

_3 detail values_:

- `server.peer_count`: $INT
- `peer.id`: $STRING
- `remove.reason`: $STRING



