# Indexing Accounts

If you're handling a large number of files (__~10k__ -> __~100k+__, depending on your system capacities) it becomes advantageous for geth to implement persistent indexing of the key files. 

:rocket: This feature is currently available on the master branch, but has not been bundled into a release (yet!), and we'll be very happy to hear your feedback if you've taken advantage of it. :eyeglasses: 

### Overview

Geth allows for a large amount of leniency in managing and interacting with key files. For example, you can have a keyfile named "foobar.md", and that's just fine. The downside of this is that it presents some inefficiencies in how geth is able to match accounts (by address) with their respective files. Geth also keeps a live sync of your `/keystore` directory, enabling you to add/change/remove files, with those changes reflected in realtime in geth. These conveniences and complications together -- combined with the innate awkwardness of handling a large number of small files (try running `ls -l` for a directory with 500k files) -- present a hurdle when the number of key files grows to a high number. 

The solutions which persistent indexing uses to address these problems are 
- disable FS watching and live reload, and
- establish geth's use of `accounts.db` as its persistent cache instead of holding file indexes in memory.

Instead of asking geth to re-index and store all key files each time a geth instance is started, it'll instead connect with a lightweight key-value store in `/keystore/accounts.db`.

### Build an index

Before running geth with the indexed accounts option, you'll first need to build the index. Run:

```shell
$ geth --verbosity 5 --index-accounts account index
```

to create an index store (_accounts.db_).

The `--verbosity` flag in this example is optional, but may be desirable for monitoring progress. You may additionally use `--data-dir`, `--chain`, and `--keystore` flags to specify custom base data directory, chain subdirectory, and keystore directory as well.

### Run with persistent indexed accounts

Once you've built an index, running:

```shell
$ geth --index-accounts
```

toggles the persistent index option. 

### Maintain your index

All calls managing key files via geth API/RPC/CLI will be __automatically updated and persisted__ in the index file. _However_, if you make outside changes to your key files, those changes __will not be synced__ to the index. In this case, _rebuilding your index -- with the exact same command as setup)_ -- will suffice to to update the index with those changes. 

### Benchmarks

_All measurements in nanoseconds/operation with watching disabled on *2.8 GHz Intel Core 2 Duo @ 8GB RAM._

#### Create, Update, Sign, and Delete a new account
This tests a bundled general suite of four interactions _without querying_. Here, in-memory is king, because the test essentially measures manipulations from memory vs. disk i/o. While persistent storage takes significantly more time (as high as 0.24 seconds for the suite), it's also worth noting that both rates behave approximately constantly across every tested number of key files.

| Number of Accounts | In-memory caching | Persistent caching |
| --- | --- | --- |
| 100 | 29147464 | 88912525 |
| 500 | 58596449 | 144582028 |
| 1000 | 32701806 | 203180542 |
| 5000 | 33743063 | 74667460 |
| 10000 | 24605203 | 133318754 |
| 20000 | 26082395 | 92159681 |
| 100000 | 21709988 | 168614805 |
| 200000 | 22346704 | 249697976 |
| 500000 | 39045149 | 161666411 |

#### Sign a key given address and passphrase
This tests a commonly used key transaction for a pre-existing key file/account. Here's where the payoff for persistent indexing comes; persisted key indexes must never be sorted or parsed entirely.

| Number of Accounts | In-memory caching | Persistent caching |
| --- | --- | --- |
| 100 | 2963462 | 2923034 |
| 500 | 2892381 | 2871620 | 
| 1000 | 5049611 | 3070000 | 
| 5000 | 5862756 | 3035130 | 
| 10000 | 5824062 | 3328642 | 
| 20000 | 724523342 | 2990753 |
| 100000 | 356307574244 | 3314435 |
| 200000 | DNF | 5204148 |
| 500000 | DNF | 3114311 |
