This page describes how to set up a local cluster of nodes, advise how to make it private, and how to hook up your nodes on the eth-netstat network monitoring app. 

A fully controlled ethereum network is useful as a backend for network integration testing (e.g. core developers working on issues related to networking/blockchain synching/message propagation, etc), or DAPP developers testing multi-block and multi-user scenarios.

We assume you've been able to build `geth` following the [install and build instructions](./home#Developers).

## Private network 

An ethereum network is a private network if the nodes are not connected to the main network nodes. In this context private only means reserved or isolated, rather than protected or secure. 

Since connections between nodes are valid only if peers have identical __protocol version and network id__, you can effectively isolate your network by setting either of these to a non default value. You can accomplish this by using the `--network-id=VALUE` flag, or by setting `"network": VALUE` in an external `chain.json` configuration file.

An external chain configuration file is recommended in order to ensure, persist, and reuse consistent node configuration.

### Customizing `chain.json`

For the sake of example, we'll set up a private network called "__privatenet__."

#### Overview
Chains can be configured and customized via a JSON file `datadir/privatenet/chain.json`. With a valid config, you can then use `geth --chain=privatenet` to toggle the implementation of that chain. 

#### Customize `chain.json`
If you run `geth --chain=privatenet` _without_ having set up a configuration file, geth will help you out a little:
```
$ geth --data-dir $HOME/eth/privatenet1 --chain privatenet

F0611 20:44:25.302614 cmd/geth/flag.go:629] chain config not found: /Users/ia/eth/privatenet1/privatenet/chain.json
        It looks like you haven't set up your custom chain yet...
        Here's a possible workflow for that:

        $ geth --chain morden dump-chain-config /Users/ia/eth/privatenet1/privatenet/chain.json
        $ sed -i.bak s/morden/privatenet/ /Users/ia/eth/privatenet1/privatenet/chain.json
        $ vi /Users/ia/eth/privatenet1/privatenet/chain.json # <- make your customizations
```

Here, geth suggests to use `morden`'s chain dump; this is only because it's a _lot_ smaller than `mainnet` (with 4 genesis allocations instead of thousands).

Please see the [README#Operating a private/custom network](https://github.com/ethereumproject/go-ethereum/blob/master/README.md#operating-a-privatecustom-network) for detailed information about each configurable JSON field.


## Setting up multiple nodes

In order to run multiple ethereum nodes locally, you have to make sure:

- each instance has a separate data directory (`--data-dir`), since both nodes will use the same `/privatenet` chain subdirectory.
- each instance runs on different ports; both eth-network and rpc (`--port` and `--rpc-port`)
- the ipc endpoints are unique, or the ipc interface is disabled (`--ipc-path` or `--ipc-disable`)
- the chains are configured identically
- in case of a cluster, the instances must know about each other

Start the first node. For this example, we'll make `port` explicit and disable the ipc interface:
```bash
geth --datadir="$HOME/eth/privatenet1"\
  --chain privatenet \
  --verbosity 6 \
  --ipc-disable \
  --port 30301 \
  --rpc-port 8101 \
  console 2>> $HOME/geth-privatenet-node-01.log
```

We started the node with the __console__ (redirecting stderr output), so that we can grab the enode url for instance:
```
> admin.nodeInfo.enode
enode://8c544b4a07da02a9ee024def6f3ba24b2747272b64e16ec5dd6b17b55992f8980b77938155169d9d33807e501729ecb42f5c0a61018898c32799ced152e9f0d7@9[::]:30301
```

`[::]` will be parsed as localhost (`127.0.0.1`). If your nodes are on a local network check each individual host machine and find your ip with `ifconfig` (on Linux and MacOS):

```bash
$ ifconfig|grep netmask|awk '{print $2}'
127.0.0.1
192.168.1.97
```

If your peers are not on the local network, you need to know your external IP address (use a service) to construct the enode url. 

Now you can launch a second node with:

```bash
cp -a $HOME/eth/privatenet1 $HOME/eth/privatenet2

geth --datadir="$HOME/eth/privatenet2" \
  --chain privatenet \
  --verbosity 6 \
  --ipcdisable \
  --port 30302 \
  --rpcport 8102 \
  console 2>> $HOME/geth-privatenet-node-02.log 
```

To connect this instance to the previously started node, you can add it as a peer from the console with `admin.addPeer(enodeUrlOfFirstInstance)`.

Test the connection by typing in either geth console:

```javascript
> net.listening
true
> net.peerCount 
1
> admin.peers
...
```

## Local cluster

As an extention of the above, you can spawn a local cluster of nodes easily. It can also be scripted including account creation which is needed for mining. 
See [`gethcluster.sh`](https://github.com/ethersphere/eth-utils) script, and the README there for usage and examples.

## Monitoring your nodes

[This page](https://github.com/ethereumproject/wiki/wiki/Network-Status) describes how to use the [The Ethereum (centralised) network status monitor (known sometimes as "eth-netstats")](http://stats.ethdev.com) to monitor your nodes.

[This page](./Setting-up-monitoring-on-local-cluster) or [this README](https://github.com/ethersphere/eth-utils) 
describes how you set up your own monitoring service for a (private or public) local cluster.