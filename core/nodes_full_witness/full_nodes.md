## Full Nodes (Witness Nodes)

#### Contents: 
- [Type of Witness nodes](/core/nodes_full_witness/full_nodes.md#type-of-witness-nodes)
   - Non-Block producing
   - Block producing
- [How to launch the full node](/core/nodes_full_witness/full_nodes.md#how-to-launch-the-full-node)
- [Configuration `config.ini`](/core/nodes_full_witness/full_nodes.md#configuration)
- [Enabling Remote Procedure Calls (RPC)](/core/nodes_full_witness/full_nodes.md#enabling-remote-procedure-calls-rpc)
- [Restarting the Witness Node](/core/nodes_full_witness/full_nodes.md#restarting-the-witness-node)
- [Public Full Node information](/core/nodes_full_witness/full_nodes.md#public-full-node-information)

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

| parameter |     |
|------|-----|
| p2p-endpoint | Endpoint for P2P node to listen on |
| seed-node  | P2P nodes to connect to on startup (may specify multiple times) |
| seed-nodes  | JSON array of P2P nodes to connect to on startup | 
| checkpoint  | Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints. |
| rpc-endpoint  |  Endpoint for websocket RPC to listen on |
| rpc-tls-endpoint  |  Endpoint for TLS websocket RPC to listen on |
| server-pem  |  The TLS certificate file for this server |
| server-pem-password  |  Password for this certificate |
| genesis-json  | File to read Genesis State from |
| dbg-init-key  | Block signing key to use for init witnesses, overrides genesis file |
| api-access  | JSON file specifying API permissions  |
| plugins  |  Space-separated list of plugins to activate |
| io-threads  | Number of IO threads, default to 0 for auto-configuration |
| enable-stale-production = false | Enable block production, even if the chain is stale. |
| required-participation = false | Percent of witnesses (0-99) that must be participating in order to produce blocks |
| witness-id  | ID of witness controlled by this node (e.g. "1.6.5", quotes are required, may specify multiple times) | 
| private-key  | Tuple of [PublicKey, WIF private key] (may specify multiple times) |
| debug-private-key   | Tuple of [PublicKey, WIF private key] (may specify multiple times) |
| track-account  |  Account ID to track history for (may specify multiple times) |
| partial-operations = 1 | Keep only those operations in memory that are related to account history tracking |
| max-ops-per-account = 1000 | Maximum number of operations per account will be kept in memory |
| elasticsearch-node-url  | Elastic Search database node url |
| elasticsearch-bulk-replay  |  Number of bulk documents to index on replay(5000) |
| elasticsearch-bulk-sync  | Number of bulk documents to index on a syncronied chain(10) |
| elasticsearch-logs  |   Log bulk events to database |
| elasticsearch-visitor  | Use visitor to index additional data(slows down the replay) |
| bucket-size = [60,300,900,1800,3600,14400,86400] | Track market history by grouping orders into buckets of equal size measured in seconds specified as a JSON array of numbers |
| history-per-size = 1000 |  How far back in time to track history for each bucket size, measured in the number of buckets (default: 1000) |
| max-order-his-records-per-market = 1000 |  Will only store this amount of matched orders for each market in order history for querying, or those meet the other option, which has more data (default: 1000) |
| max-order-his-seconds-per-market = 259200 | Will only store matched orders in last X seconds for each market in order history for querying, or those meet the other option, which has more data (default: 259200 (3 days)) |
| trusted-node  |  RPC endpoint of a trusted validating node (required) |
| snapshot-at-block  |   Block number after which to do a snapshot |
| snapshot-at-time  | Block time (ISO format) after which to do a snapshot |
| snapshot-to  |  Pathname of JSON file where to store the snapshot |
| es-objects-elasticsearch-url  |  Elasticsearch node url  |
| es-objects-logs  | Log bulk events to database | 
| es-objects-bulk-replay  | Number of bulk documents to index on replay(5000) |
| es-objects-bulk-sync  |  Number of bulk documents to index on a syncronied chain(10) |
| es-objects-proposals  |  Store proposal objects |
| es-objects-accounts  | Store account objects  |
| es-objects-assets  | Store asset objects |
| es-objects-balances  | Store balances objects |
| es-objects-limit-orders  | Store limit order objects |
| es-objects-asset-bitasset | Store feed data |
| tracked-groups = [10,100] | Group orders by percentage increase on price. Specify a JSON array of numbers here, each number is a group, number 1 means 0.01%. |


|  | 
|---|
| declare an appender named "stderr" that writes messages to the console |
| **[log.console_appender.stderr]** |  
| - stream=std_error | 
| ------------------------- |  
| declare an appender named "default" that writes messages to default.log  |
| **[log.file_appender.default]** |  
| *filename can be absolute or relative to this config file*  <br/> - filename=logs/default/default.log  |
| *Rotate log every ? minutes, if leave out default to 60* <br/> - rotation_interval=60  |
| *how long will logs be kept (in days), if leave out default to 1* <br/> - rotation_limit=7  |
| ------------------------- |  
| declare an appender named "p2p" that writes messages to p2p.log |
| **[log.file_appender.p2p]** |  
|  *filename can be absolute or relative to this config file* <br/> - filename=logs/p2p/p2p.log |
| *Rotate log every ? minutes, if leave out default to 60*  <br/> - rotation_interval=60 | 
| *how long will logs be kept (in days), if leave out default to 1* <br/> - rotation_limit=7 |
| ------------------------- |  
| declare an appender named "rpc" that writes messages to rpc.log |
| **[log.file_appender.rpc]** |  
| *filename can be absolute or relative to this config file*  <br/> - filename=logs/rpc/rpc.log |
| *Rotate log every ? minutes, if leave out default to 60*  <br/> - rotation_interval=60 |
| *how long will logs be kept (in days), if leave out default to 1* <br/> - rotation_limit=7 |
| ------------------------- |  
| route any messages logged to the default logger to the "stderr" appender and "default" appender we declared above, if they are info level or higher |
| **[logger.default]** | 
| - level=info  <br/> - appenders=stderr,default |  
| ------------------------- |  
| route messages sent to the "p2p" logger to the "p2p" appender declared above |  
| **[logger.p2p]** |  
| - level=warn <br/> - appenders=p2p |  
| ------------------------- |  
| route messages sent to the "rpc" logger to the "rpc" appender declared above |  
| **[logger.rpc]**  |  
| - level=error <br/> - appenders=rpc |  


***

### Enabling Remote Procedure Calls (RPC)

In order to allow RPC calls for blockchain operations you need to modify the following entry in the configuration file::

    rpc-endpoint = 0.0.0.0:8090

This will open the port 8090 for global queries only. Since the witness node only maintains the blockchain and (unless you are an actively block producing witness) no private keys are involved, it is safe to expose your witness to the internet.

### Restarting the Witness Node

When restarting the witness node, it may be required to append the `--replay-blockchain` parameter to regenerate the local (in-memory) blockchain state.

### Public Full Node information 

- Here is the latest list of [Public Full Node](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67).


---


(ref)

- http://docs.bitshares.org/integration/apps/node.html
