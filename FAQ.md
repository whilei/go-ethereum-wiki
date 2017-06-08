**Q.**  I noticed my peercount slowly decrease, and now it is at 0.  Restarting doesn't get any peers.

**A.**  Check and sync your clock with ntp.  [Example](http://askubuntu.com/questions/254826/how-to-force-a-clock-update-using-ntp) `sudo ntpdate -s time.nist.gov`

If this doesn't help, please review the [Connecting To the Network Wiki page](./Connecting-to-the-network).

----
**Q.**  I would like to run multiple geth instances but got the error "Fatal: blockchain db err: resource temporarily unavailable".

**A.**  Geth uses a datadir to store the blockchain, accounts and some additional information. This directory cannot be shared between running instances. If you would like to run multiple instances follow [these](https://github.com/ethereumproject/go-ethereum/wiki/Setting-up-private-network-or-local-cluster) instructions.

----
**Q.** I sent a transaction, but it's forever 'pending'. I see no errors, but geth doesn't seem to be processing the transaction.

**A.** Check your transaction's `nonce` value. If a transaction with a nonce is submitted with a "too high" nonce value, geth will hold the transaction in memory and wait until it receives a transaction with a correct nonce to insert before yours. Nodes can receive transactions asynchronously, so there is no way to check if a nonce is (or will be) missing completely.

----
**Q.** What is the difference between `chain id`, `chain identity`, and `network id`?

**A.**
- `chain id` is used by the chain configuration for signing replay-protected (see [EIP155](todo)) transactions. It is configured in the "Diehard" upgrade fork.
- `chain identity` is a local value, and is not a part of the p2p or blockchain protocols, per se. With default values "mainnet" and "morden", it can be used as an argument to specify a custom chain. When doing so, the configuration file JSON key "identity" must match the containing subdirectory (`/datadir/customnet/chain.json`) to ensure your configurations are in proper order.
- `network id` is used in the p2p protocol to identify valid peers on a given network. Mainnet network id is 1, Morden is 2, and your private net can be any positive integer (although best not 1 or 2, to avoid trying unuseful peers). 
