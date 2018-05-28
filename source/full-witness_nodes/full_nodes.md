## Full Nodes (Witness Nodes)

#### Contents: 
- [Type of Witness nodes](/source/full-witness_nodes/full_nodes.md#type-of-witness-nodes)
   - Non-Block producing
   - Block producing
- [How to launch the full node](/source/full-witness_nodes/full_nodes.md#how-to-launch-the-full-node)
- [Configuration](/source/full-witness_nodes/full_nodes.md#configuration)
- [Enabling Remote Procedure Calls (RPC)](/source/full-witness_nodes/full_nodes.md#enabling-remote-procedure-calls-rpc)
- [Restarting the Witness Node](/source/full-witness_nodes/full_nodes.md#restarting-the-witness-node)


***

### Type of Witness nodes:

* **Non-block producing** witness nodes
* **Block producing** witness nodes (more requirements and duties)

We distinguish between full nodes (a.k.a. non-block producing witness nodes) and block producing witness nodes. Both are implemented by the same executable but the latter requires some additional parameters to be defined and the corresponding witness voted active by the BTS Holders.

Both represent nodes in the network that verify all transactions and blocks against the current state of the overall network. Hence, we recommend all service providers to run and maintain their own full nodes for reliability and security reasons.

### How to launch the full node

    ./programs/witness_node/witness_node

It takes an optional –data-dir parameter to define a working and data directory to store the configuration, blockchain and local databases (defaults to `witness_node_data_dir`). Those will be automatically created with default settings if they don’t exist locally set.

### Configuration

The configuration file `config.ini` in your data directory is commented and contains the following essential settings:

| item |     |
|------|--------|
| p2p-endpoint | Endpoint for P2P node to listen on |
| seed-node | P2P nodes to connect to on startup (may specify multiple times) |
| checkpoint | Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints. |
| rpc-endpoint | Endpoint for websocket RPC to listen on (e.g. 0.0.0.0:8090) |
| rpc-tls-endpoint | Endpoint for TLS websocket RPC to listen on |
| server-pem | The TLS certificate file for this server |
| server-pem-password  |Password for this certificate  |
| genesis-json | File to read Genesis State from |
| api-access | JSON file specifying API permissions |
| enable-stale-production | Enable block production, even if the chain is stale. (unless for private testnets should be false) |
| required-participation | Percent of witnesses (0-99) that must be participating in order to produce blocks |
| allow-consecutive | Allow block production, even if the last block was produced by the same witness. |
| witness-id  | ID of witness controlled by this node (e.g. “1.6.5”, quotes are required, may specify multiple times) |
| private-key | Tuple of [PublicKey, WIF private key] (may specify multiple times) |
| track-account | Account ID to track history for (may specify multiple times) |
| bucket-size | Track market history by grouping orders into buckets of equal size measured in seconds specified as a JSON array of numbers |
| history-per-size | How far back in time to track history for each bucket size, measured in the number of buckets (default: 1000) |

> Note: Folders and files are considered to be relative to the working directory (i.e. the directory from which the executables are launched from)


### Enabling Remote Procedure Calls (RPC)

In order to allow RPC calls for blockchain operations you need to modify the following entry in the configuration file::

    rpc-endpoint = 0.0.0.0:8090

This will open the port 8090 for global queries only. Since the witness node only maintains the blockchain and (unless you are an actively block producing witness) no private keys are involved, it is safe to expose your witness to the internet.

### Restarting the Witness Node

When restarting the witness node, it may be required to append the `--replay-blockchain` parameter to regenerate the local (in-memory) blockchain state.


---


(ref)

- http://docs.bitshares.org/integration/apps/node.html

