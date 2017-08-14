# mlog Spec v0.1.0

The purpose of `mlog` is to enable programmatic reporting and analysis of geth behavior through machine-readable log files.

> _Note on naming_: __mlog__ stands for `machine log`, and echoes the existing Google Log package `glog`, which is currently implemented as the primary user-facing logging system for geth.

### Usage across geth components

An `mlog` instance of type `*logger.Logger` should be implemented separately for each geth component package, _eg_ `p2p`, `node`, `eth`... This allows us to keep the system modular, use component-specific log line prefixes, and "rollout" implementations for each package as the work is completed.

The `mlog` implementation will begin with a focus on peer discovery, within package `p2p`.

### User configuration

There should be a boolean flag to enable/disable `mlog` logging:

```
--mlog=true
```

The default setting should be `true`; passing `--mlog=false` will disable it.

### Levels

Log levels should __not__ be used. In the code, this means all `mlog` verbosity levels should be set to `Error`. Instead of configurable verbosity, errors should be logged via an identifiable `error` pattern in the log content. See _Appendix_ for further discussion.


### Line formatting

Log lines are intended to interact as efficiently as possible with both commercial and custom file parsing software.

A guideline specification should be established as a common reference across geth components. This will build common language for placeholder names and suggestions for value ordering.

Each component `mlog` implementation should create and document a line format specification for each available log line. For example: _log lines should always begin with a value `$DATE`, where `$DATE` is of the form `%Y/%m/%d`_.

See _Appendix_ for a specification proposal.

Log line formatting __documention__ for available datum should adhere to a standard form. Likewise, see _Appendix_ for a specification proposal.


### Filesystem

Logs will be stored in a folder `mlogs/` located immediately beneath the `<datadir>`. One log file will be created and appended to per session with a guaranteed-unique name.

#### Directory

_Generally_:
```
<datadir>/mlogs/
```

_Example_:
```
/Users/ia/Library/EthereumClassic/mlogs/
```

#### Files

All geth components will log to the same file.

The file name should include an alphabetically-sortable timestamp. Each file name should be of the form:

_Generally_:
```
<programname>.<hostname>.<username>.mlog.<YYYY><MM><DD>-<hh><mm><ss>.<pid>
```

_Example_:
```
geth.mh.ia.mlog.20170731-154537.76710
```

Log files should automatically include a header detailing
available runtime and geth context information, for example:

```
Log file created at: 2017/08/03 09:30:36
Running on machine: mh
Binary: Built with gc go1.8 for darwin/amd64
-------------------------------------------------------------
```

----

# Appendix

## Discussions

### Log levels:

An example of __log levels__ are:

- `Error` = 1
- `Warn` = 2
- `Info` = 3
- `Debug` = 4
- `Detail` = 5

Log levels are log metadata. They enable and are designed for user-facing systems. They allow the user to configure and filter log verbosity by priority; relying on the programmer to make a sensible decision about what qualifies as a "warning" versus an "info," for example.

This is not desirable for machine-oriented output, which should be as predictable and consistent as possible, behaving as a stable API. The information from log _metadata_ should instead be moved to log _data_, ie. written as parseable content in the log line.

This eliminates arbitrary prioritization of information on the part of programmer, and the need to explain or document what defines an "Error," what qualifies as a "Detail," and so forth.

This _does not imply_ that errors will not be logged OR that priority data can not be included.

## Proposals

### Filesystem directory configuration

- [ ] __Piggy-back existing flag__ `--log-dir`. For example, if `--log-dir=/var/log/geth-classic` flag is passed, then machine logs will be stored in `/var/log/geth-classic/mlog/`.
- [x] __Create flag__ `--mlog-dir`.

### General log line formatting

#### documentation spec (yea, a spec<sup>spec</sup>):

- Date and time formatting SHOULD be defined by C-lang _strftime_ interpolation characters.
- Documented abstract values for _variables_ SHOULD be appropriate variable names prefixed with `$`, eg. `$TRANSFERRED_BYTES`
- Documented abstract values for _constant literals_ SHOULD NOT be prefixed, eg. `PEER`


#### line spec:

- SHOULD have a value `$DATE` at POSITION_1, where `$DATE` is of the form `%Y/%m/%d`
- SHOULD have a value `$TIME` at POSITION_2, where `$TIME` is of the form `%H:%M:%S`
SHOULD have a value `$COMPONENT` at POSITION_3, where `$COMPONENT` is of the form `[cmp]`, where `cmp` is probably the name of what in Go is called a "package"; a grouping of related code used to solve a similar set of problems
- SHOULD have a following value `$RECEIVER`
- SHOULD have a following value `$VERB`
- SHOULD have a following value `$SUBJECT`
- SHOULD have following distinct values `$PARENT:DETAIL1`, `$PARENT:DETAIL2`, where "PARENT" describes what the detail pertains to

A loose heuristic for defining abstract classes `RECEIVER`, `VERB`, and `SUBJECT` can be made by referencing the related function signature:

```go
func (p *Peer) send(msg Msg) {
	mlog.Sendf(1, "PEER SEND MSG %v %v", x, y)
	...
}
```

In this case:

- `Peer` is the RECEIVER
- `send` is the VERB
- `msg` is the SUBJECT



#### mlog line case examples:

_Generally_:

&lt;Datum title&gt;

> Title of the log datum

&lt;Description&gt;

> Brief description of context and relevance for datum

`$<VAL_NAME>` `<VAL_NAME>` `$<VAL_NAME>`

 > Space-separated list of arbitrary values by name. Use `$DOLLAR_PREFIXED_VARS` to signify variable values, and `NONPREFIXED_VARS` to signify literal constants.
 > Each detail should be surrounded by brackets. This improves ease of parsing for strings that contains spaces, like error messages.

```
$DATE $TIME [$cmp] RECEIVER VERB SUBJECT [$RECEIVER:DETAIL] [$RECEIVER:DETAIL] [$SUBJECT:DETAIL] [$SUBJECT:DETAIL]
```

> An example with real data

- `$RECEIVER:DETAIL` is the DETAIL of RECEIVER
- `$SUBJECT:DETAIL` is the DETAIL of of SUBJECT
- ... ETC.

> An ordered bullet list describing DETAIL variables and any relevant formatting or contextual details. Don't forget to mention how absent, error, or nil values will be represented for DETAILS and FINGERPRINT names. There is no need to explain header constants $DATE thru SUBJECT.

_Example_:

Peer ping send

Fired once for each outgoing message of form `msgPing` to a neighbor. Used to determine peer availability and network latency.

`$DATE` `$TIME` `p2p` `PEER` `SEND` `PING` `$PEER:IP` `$PEER:PORT` `$PEER:ID` `$PING:TRANSFERRED_BYTES`

```
2017/08/01 13:50:23 [p2p] PEER SEND PING [123.45.67.89] [30303] [7909d51011d8a153] [252]
```

- `123.45.67.89` is a DETAIL. It is the IPv4 address of the ping request
- `30303` is a DETAIL. It is the v4 IP port of the peer sent the ping request
- `7909d51011d8a153` is a DETAIL. It is the node ID of the peer sent the ping request
- `252` is a DETAIL. It is the number of bytes transferred belonging to the ping request
- Missing FINGERPRINT values are represented with `-`
