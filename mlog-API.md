# mlog API

`mlog` stands for "machine logging." To read the spec for mlog implementation
and documenation, please visit the [mlog spec](./mlog-Spec) page.

mlog has the following option flags:



- `--mlog` (default:`kv`): specifies format in which mlog LINES should be structured, possibly format values are `kv` (key-value), `json`, and `plain` (same as key-value, but with just values), as well as `off` to disable mlog entirely
- `--mlog-dir` (default: `<datadir>/<chain-identity/mlogs`): directory to hold mlogs
- `--mlog-components` (default: all available): comma-separated list of components which should be logged. For a list of available components, please see the [LINES reference section](#lines).

#### Overview

Machine logging is enabled by default. To disable it, use `--mlog=off`.

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

#### LINE structure

A LINE is the result of a single event in the go-ethereum protocol. For brevity, the following structure documentation refers to `plain` and `kv`-formatted LINES. For notes on `json`-formatting, please refer to the section below.

LINES are organized with a common structure:

```
$DATE $TIME [$cmp] RECEIVER VERB SUBJECT $RECEIVER:DETAIL $RECEIVER:DETAIL $SUBJECT:DETAIL $SUBJECT:DETAIL
```

where the number and types of details vary for different lines.

For example:
```
2017/08/01 13:50:23 [discover] PING HANDLE FROM 123.45.67.89:30303 7909d51011d8a153 252
```

LINE structure can be understood in parts:

__Header__: `2017/08/01 13:50:23 [discover]`

The first value is the __date__, of the form `%Y/%m/%d`. The second value is the __time__, of the form `%H:%M:%S`.
The third value is the trace __component__ calling the log. The component name will always be in brackets. Header structure is the same for all `kv` and `plain` LINES, but is excluded from `json`-formatted LINES.

__Fingerprint__: `PING HANDLE FROM`

LINES are referred to and organized by their unique fingerprint pattern: `RECEIVER VERB SUBJECT`.
For `json`-formatted LINES, this pattern will be present within the logged json objects as
`"event": "receiver.subject.verb"`.


__Details__: `123.45.67.89:30303 7909d51011d8a153 252`

_Note_: In this documentation, LINE variable names which represent dynamic values (eg. "$DATE")
are prefixed with `$`, while _constant_ values (eg. "SEND")
are _not_ prefixed.

All DETAIL values are wrapped in brackets to improve ease of parsing, especially when the value is a string that may contain spaces, eg. `[empty header received]`.

#### JSON-formatted LINE structure

__Flatness__

Each LINE logged in JSON format contains a single JSON object, which should be as "flat" as possible; instead of presenting:

```json
{"node": {"ip": "1234asdf", "port": 3333}}
```

the LINE will prefer a format like:

```json
{"node.ip": "1234asdf", "node.port": 3333}
```

__Additional keys__

Each JSON-formatted LINE contains two additional keys:

- `"event"`: the 'fingerprint' of the LINE event. This, for example, may be used as the `json.event_key` value for Logstash.
- `"ts"`: the timestamp of the event. Note that this is the time at which the event was logged,
not the time at which the LINE may be ingested by a log parsing program. The value will be in the form `2017-08-10T15:20:11.294317237-05:00`.

# LINES

| Component | Description |
| --- | --- |
| discover | The discover component handles low-level peer discovery requests. All calls are made from file `/p2p/discover/udp.go`, and available mlog variables for the package can be found in `/p2p/discover/mlog.go`. |
| blockchain | The blockchain component tracks block and chain insertions into the chaindb during import and sync. |
| server | The server component tracks peer (p2p) registrations. |



[discover]

- [ping.handle.from](#ping-handle-from)
- [ping.send.to](#ping-send-to)
- [pong.handle.from](#pong-handle-from)
- [pong.send.to](#pong-send-to)
- [findnode.handle.from](#findnode-handle-from)
- [findnode.send.to](#findnode-send-to)
- [neighbors.handle.from](#neighbors-handle-from)
- [neighbors.send.to](#neighbors-send-to)

[blockchain]

- [blockchain.write.block](#blockchain-write-block)
- [blockchain.insert.blocks](#blockchain-insert-blocks)

[server]

- [server.add.peer](#server-add-peer)
- [server.remove.peer](#server-remove-peer)

----


#### PING HANDLE FROM
Called once for each received PING request from peer FROM.

__Key value__:
```
2017/08/11 13:11:58 [discover] PING HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] ping.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"ping.handle.from","from.id":"string","from.udp_address":"string","ping.bytes_transferred":0,"ts":"2017-08-11T13:11:58.12694453-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] PING HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `ping.bytes_transferred`: $INT



#### PING SEND TO
Called once for each outgoing PING request to peer TO.

__Key value__:
```
2017/08/11 13:11:58 [discover] PING SEND TO to.udp_address=[$STRING] ping.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"ping.send.to","ping.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-11T13:11:58.127378018-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] PING SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `ping.bytes_transferred`: $INT



#### PONG HANDLE FROM
Called once for each received PONG request from peer FROM.

__Key value__:
```
2017/08/11 13:11:58 [discover] PONG HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] pong.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"pong.handle.from","from.id":"string","from.udp_address":"string","pong.bytes_transferred":0,"ts":"2017-08-11T13:11:58.127419524-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] PONG HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `pong.bytes_transferred`: $INT



#### PONG SEND TO
Called once for each outgoing PONG request to peer TO.

__Key value__:
```
2017/08/11 13:11:58 [discover] PONG SEND TO to.udp_address=[$STRING] pong.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"pong.send.to","pong.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-11T13:11:58.127472571-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] PONG SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `pong.bytes_transferred`: $INT



#### FINDNODE HANDLE FROM
Called once for each received FIND_NODE request from peer FROM.

__Key value__:
```
2017/08/11 13:11:58 [discover] FINDNODE HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] findnode.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"findnode.handle.from","findnode.bytes_transferred":0,"from.id":"string","from.udp_address":"string","ts":"2017-08-11T13:11:58.127521961-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] FINDNODE HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `findnode.bytes_transferred`: $INT



#### FINDNODE SEND TO
Called once for each received FIND_NODE request from peer FROM.

__Key value__:
```
2017/08/11 13:11:58 [discover] FINDNODE SEND TO to.udp_address=[$STRING] findnode.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"findnode.send.to","findnode.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-11T13:11:58.127561515-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] FINDNODE SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `findnode.bytes_transferred`: $INT



#### NEIGHBORS HANDLE FROM
Called once for each received NEIGHBORS request from peer FROM.

__Key value__:
```
2017/08/11 13:11:58 [discover] NEIGHBORS HANDLE FROM from.udp_address=[$STRING] from.id=[$STRING] neighbors.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"neighbors.handle.from","from.id":"string","from.udp_address":"string","neighbors.bytes_transferred":0,"ts":"2017-08-11T13:11:58.127602615-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] NEIGHBORS HANDLE FROM [$STRING] [$STRING] [$INT]
```

_3 detail values_:

- `from.udp_address`: $STRING
- `from.id`: $STRING
- `neighbors.bytes_transferred`: $INT



#### NEIGHBORS SEND TO
Called once for each outgoing NEIGHBORS request to peer TO.

__Key value__:
```
2017/08/11 13:11:58 [discover] NEIGHBORS SEND TO to.udp_address=[$STRING] neighbors.bytes_transferred=[$INT]
```

__JSON__:
```json
{"event":"neighbors.send.to","neighbors.bytes_transferred":0,"to.udp_address":"string","ts":"2017-08-11T13:11:58.127662713-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [discover] NEIGHBORS SEND TO [$STRING] [$INT]
```

_2 detail values_:

- `to.udp_address`: $STRING
- `neighbors.bytes_transferred`: $INT



#### BLOCKCHAIN WRITE BLOCK
Called when a single block written to the chain database.
A STATUS of NONE means it was written _without_ any abnormal chain event, such as a split.

__Key value__:
```
2017/08/11 13:11:58 [blockchain] BLOCKCHAIN WRITE BLOCK write.status=[$STRING] write.error=[$STRING_OR_NULL] block.number=[$BIGINT] block.hash=[$STRING] block.size=[$INT64] block.transactions_count=[$INT] block.gas_used=[$BIGINT] block.coinbase=[$STRING] block.time=[$BIGINT]
```

__JSON__:
```json
{"block.coinbase":"string","block.gas_used":0,"block.hash":"string","block.number":0,"block.size":null,"block.time":0,"block.transactions_count":0,"event":"blockchain.write.block","ts":"2017-08-11T13:11:58.1277158-05:00","write.error":null,"write.status":"string"}
```

__Plain__:
```
2017/08/11 13:11:58 [blockchain] BLOCKCHAIN WRITE BLOCK [$STRING] [$STRING_OR_NULL] [$BIGINT] [$STRING] [$INT64] [$INT] [$BIGINT] [$STRING] [$BIGINT]
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
2017/08/11 13:11:58 [blockchain] BLOCKCHAIN INSERT BLOCKS blocks.count=[$INT] blocks.queued=[$INT] blocks.ignored=[$INT] blocks.transactions_count=[$INT] blocks.last_number=[$BIGINT] blocks.first_hash=[$STRING] blocks.last_hash=[$STRING] insert.time=[$DURATION]
```

__JSON__:
```json
{"blocks.count":0,"blocks.first_hash":"string","blocks.ignored":0,"blocks.last_hash":"string","blocks.last_number":0,"blocks.queued":0,"blocks.transactions_count":0,"event":"blockchain.insert.blocks","insert.time":null,"ts":"2017-08-11T13:11:58.127810888-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [blockchain] BLOCKCHAIN INSERT BLOCKS [$INT] [$INT] [$INT] [$INT] [$BIGINT] [$STRING] [$STRING] [$DURATION]
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



#### SERVER ADD PEER
Called once when a peer is added.

__Key value__:
```
2017/08/11 13:11:58 [server] SERVER ADD PEER server.peer_count=[$INT] peer.id=[$STRING] peer.remote_addr=[$STRING] peer.remote_version=[$STRING]
```

__JSON__:
```json
{"event":"server.add.peer","peer.id":"string","peer.remote_addr":"string","peer.remote_version":"string","server.peer_count":0,"ts":"2017-08-11T13:11:58.127908132-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [server] SERVER ADD PEER [$INT] [$STRING] [$STRING] [$STRING]
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
2017/08/11 13:11:58 [server] SERVER REMOVE PEER server.peer_count=[$INT] peer.id=[$STRING] remove.reason=[$STRING]
```

__JSON__:
```json
{"event":"server.remove.peer","peer.id":"string","remove.reason":"string","server.peer_count":0,"ts":"2017-08-11T13:11:58.127965663-05:00"}
```

__Plain__:
```
2017/08/11 13:11:58 [server] SERVER REMOVE PEER [$INT] [$STRING] [$STRING]
```

_3 detail values_:

- `server.peer_count`: $INT
- `peer.id`: $STRING
- `remove.reason`: $STRING
