# mlog API

`mlog` stands for "machine logging." To read the spec for mlog implementation
and documenation, please visit the [mlog spec](./Mlog-Spec.md) page.

Related option flags:

| flag | default | usage |
| --- | --- | --- |
| `--mlog` | true | enable/disable mlogging |
| `--mlog-dir` | <datadir>/mlogs/ | directory to hold mlogs |

#### Overview

__mlogs are enabled by default__. To disable them, use flag `--mlog=false`.

`mlog` files are automatically generated in the subdirectory `/mlogs` of the data directory of the running
geth instance, for example:

```
$HOME/Library/EthereumClassic/mlogs/
```

To customize this location, use flag `--mlog-dir=/path/to/mlogs`, where `/path/to/mlogs` may be an absolute or relative path to a directory. If the directory or any of it's parents do not exist, they will be created, _ala_ `mkdir -p`.

mlog files are programmatically named based on context and time. The latest
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

`LINE` and `LINES` refer to a "unit" of log information, namely a "kind of
  line." A LINE is the result of a single event in the go-ethereum protocol. All LINE values are space-separated.

LINES are organized with a common structure:

```
$DATE $TIME [$cmp] RECEIVER VERB SUBJECT $RECEIVER:DETAIL $RECEIVER:DETAIL $SUBJECT:DETAIL $SUBJECT:DETAIL
```

For example:
```
2017/08/01 13:50:23 [discover] PING HANDLE FROM 123.45.67.89:30303 7909d51011d8a153 252
```

LINE structure can be understood in parts:

__Header__: `2017/08/01 13:50:23 [discover]`

The first value is the __date__, of the form `%Y/%m/%d`. The second value is the __time__, of the form `%H:%M:%S`.
The third value is the trace __component__ calling the log. The component name will always be in brackets.

__Fingerprint__: `PING HANDLE FROM`

LINES are referred to and organized by their unique fingerprint pattern: `RECEIVER VERB SUBJECT`.


__Details__: `123.45.67.89:30303 7909d51011d8a153 252`

In this documentation, LINE variable names which represent dynamic values (eg. "$DATE")
are prefixed with `$`, while _constant_ values (eg. "SEND")
are _not_ prefixed.

A LINE variable representing a DETAIL (eg. `$NODE:IP`) represents a dynamic attribute
variable IP belonging to the NODE data.

## LINES

| Component | Tag | Description |
| --- | --- | --- |
| [discover](#discover) | [discover] | The discover component handles low-level peer discovery requests. All calls are made from file `/p2p/discover/udp.go`, and available mlog variables for the package can be found in `/p2p/discover/mlog.go`. |


### [discover]

__PING__ is a small request used to check availability and latency for a node.
__PONG__ is an answer to a PING. Marco Polo, anyone?

__FINDNODE__ is a request asking for neighbors.
__NEIGHBORS__ is the response containing a set of nodes, often sent in "chunks"
to keep packet size small enough.


- PING
  + [PING HANDLE FROM](#PING-HANDLE-FROM)
  + [PING SEND TO](#PING-SEND-TO)
- PONG
  + [PONG HANDLE FROM](#PONG-HANDLE-FROM)
  + [PONG SEND TO](#PONG-SEND-TO)
- FINDNODE
  + [FINDNODE HANDLE FROM](#FINDNODE-HANDLE-FROM)
  + [FINDNODE SEND TO](#FINDNODE-SEND-TO)
- NEIGHBORS
  + [NEIGHBORS HANDLE FROM](#NEIGHBORS-HANDLE-FROM)
  + [NEIGHBORS SEND TO](#NEIGHBORS-SEND-TO)


## LINE documentation

#### PING HANDLE FROM
Called once for each received PING request from peer FROM.

`$DATE` `$TIME` `discover` `PING` `HANDLE` `FROM` `$FROM:IP` `$FROM:ID` `$PING:BYTES_TRANSFERRED`

```
2017/08/02 12:05:18 [discover] PING HANDLE FROM 81.225.200.55:30303 7c8a5fe77a58127815fd450b443be0129846a83dffab2c3c4d9577f5a6cdfdab96973492b31c0c5183ece6ff98247a45ad1228917ed886c64af3f16859d6ff05 159
```

- `81.225.200.55:30303` (_STRING_) is the address of the originating node FROM
- `7c8a5fe...` (_STRING_) is the ID of the originating node FROM
- `159` (_INT_) is the number of bytes transferred in the request

#### PING SEND TO
Called once for each outgoing PING request to peer TO.

`$DATE` `$TIME` `discover` `PING` `SEND` `TO` `$TO:IP` `$PING:BYTES_TRANSFERRED`

```
2017/08/02 12:05:18 [discover] PING SEND TO 81.225.200.55:30303 159
```

- `81.225.200.55:30303` (_STRING_) is the address of the target node TO
- `159` (_INT_) is the number of bytes transferred in the request

#### PONG HANDLE FROM
Called once for each received PONG request from peer FROM.

`$DATE` `$TIME` `discover` `PONG` `HANDLE` `FROM` `$FROM:IP` `$FROM:ID` `$PONG:EXPIRED`

```
2017/08/02 12:05:18 [discover] PONG HANDLE FROM 81.225.200.55:30303 7c8a5fe77a58127815fd450b443be0129846a83dffab2c3c4d9577f5a6cdfdab96973492b31c0c5183ece6ff98247a45ad1228917ed886c64af3f16859d6ff05 159
```

- `81.225.200.55:30303` (_STRING_) is the address of the originating node FROM
- `7c8a5fe...` (_STRING_) is the ID of the originating node FROM
- `159` (_INT_) is the number of bytes transferred in the request

#### PONG SEND TO
Called once for each outgoing PONG request to peer TO.

`$DATE` `$TIME` `discover` `PONG` `SEND` `TO` `$TO:IP` `$PONG:BYTES_TRANSFERRED`

```
2017/08/02 12:05:18 [discover] PONG SEND TO 81.225.200.55:30303 159
```

- `81.225.200.55:30303` (_STRING_) is the address of the target node TO
- `159` (_INT_) is the number of bytes transferred in the request

#### FINDNODE HANDLE FROM
Called once for each received FINDNODE request from peer FROM.

`$DATE` `$TIME` `discover` `FINDNODE` `HANDLE` `FROM` `$FROM:IP` `$FROM:ID` `$FINDNODE:EXPIRED`

```
2017/08/02 12:05:18 [discover] FINDNODE HANDLE FROM 81.225.200.55:30303 7c8a5fe77a58127815fd450b443be0129846a83dffab2c3c4d9577f5a6cdfdab96973492b31c0c5183ece6ff98247a45ad1228917ed886c64af3f16859d6ff05 159
```

- `81.225.200.55:30303` (_STRING_) is the address of the originating node FROM
- `7c8a5fe...` (_STRING_) is the ID of the originating node FROM
- `159` (_INT_) is the number of bytes transferred in the request

#### FINDNODE SEND TO
Called once for each outgoing FINDNODE request to peer TO.

`$DATE` `$TIME` `discover` `FINDNODE` `SEND` `TO` `$TO:IP` `$FINDNODE:BYTES_TRANSFERRED`

```
2017/08/02 12:05:18 [discover] FINDNODE SEND TO 81.225.200.55:30303 159
```

- `81.225.200.55:30303` (_STRING_) is the address of the target node TO
- `159` (_INT_) is the number of bytes transferred in the request

#### NEIGHBORS HANDLE FROM
Called once for each received NEIGHBORS request from peer FROM.

`$DATE` `$TIME` `discover` `NEIGHBORS` `HANDLE` `FROM` `$FROM:IP` `$FROM:ID` `$NEIGHBORS:EXPIRED`

```
2017/08/03 09:32:08 [discover] NEIGHBORS HANDLE FROM 159.122.120.218:30303 eea24304e967589f557916716464dd388746a51456b906576aabfe1cc13789f458cddaf92c3e7d391a74f4c8499589f2adab1aa37d436c2b07afafb50e78f399 425
```

- `159.122.120.218:30303` (_STRING_) is the address of the originating node FROM
- `eea24304...` (_STRING_) is the ID of the originating node FROM
- `425` (_INT_) is the number of bytes transferred in the request

#### NEIGHBORS SEND TO
Called once for each outgoing NEIGHBORS request to peer TO.

`$DATE` `$TIME` `discover` `NEIGHBORS` `SEND` `TO` `$TO:IP` `$NEIGHBORS:BYTES_TRANSFERRED`

```
2017/08/03 09:32:10 [discover] NEIGHBORS SEND TO 5.189.181.133:1274 1057
```

- `5.189.181.133:1274` (_STRING_) is the address of the target node TO
- `1057` (_INT_) is the number of bytes transferred in the request
