# BitShares Nodes 

#### Table of Contents:
- [BitShares Nodes and P2P](../nodes_full_witness/README.md#bitshares-nodes-and-p2p-network)
- [Full Node (Witness Nodes)](../nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)
  : Type of Witness nodes, System Requirements, How to launch, Configuration 
- [Running a BitShares API Node](../nodes_full_witness/running-api-node.md#running-a-bitshares-api-node)   
- [Become an Active Witness](../nodes_full_witness/active_witness.md#become-an-active-witness)
 : A Block-Producing Witness set up, Backup Server, Price Feeds
- FAQ
  - [Committee](../tutorials/FAQ.md#committee)
  - [Witness](../tutorials/FAQ.md#witness)


***

## BitShares Nodes and P2P network

#### What is the Nodes?
According to Wikipedia, in Computer Science, 
 > A node is a basic unit used in computer science. Nodes are devices or data points on a more extensive network. Devices such as a personal computer, cell phone, or printer are nodes. When defining nodes on the internet, a node is anything that has an IP address.  
From <https://en.wikipedia.org/wiki/Node_(computer_science)> 

### BitShares Client and Node

The BitShares client uses a peer-to-peer network. Each client connects to the BitShares Full Node to interact with the network to query the data and broadcast messages/transactions directly. If you are using a command prompt screen, you might try some API calls to query BitShares Blockchain data after you connect to a full node. 

### Witness Node and Full Node  

When we talk about the BitShares Nodes, we use **witness node** and **full node** terms almost interchangeably.  However, there are some different roles between them. The "witness node" works as a **block producer**. The "full node" works as a **non-block producer** and mainly provides an API service which interacts with BitShares clients and applications and queries the BitShares Blockchain data. 

- [Types of nodes](../nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)

Both are implemented by the executable (*witness_node*), but in the command lines extra parameters can represent differences each node operation.  For example, a (block producing) witness node has been provided an authorized block-signing private key, on the other hand, a (non-block producing) full node is not.  However, both *interact* with the decentralized network (blockchain). 

#### BitShares Public Full Nodes
If you do not have own node to run or connect, you can use one of the BitShares Public API services (public full nodes) to interact with the BitShares blockchain. 

- The latest list of [BitShares Public Full Nodes](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information.

### Witness Node's Roles and Duties

The witness node has a copy of the full BitShares blockchain data (transaction history) on a computer or other hardware devices.  

Anyone who wishes to have own copy of the blockchain data can do so. However, that would take hours (days) to download and sync the data and require many memories to run the blockchain (*Check here [BitShares updated System Requirements](../nodes_full_witness/full_nodes.md#system-requirements)*). Therefore, anyone who is interested in being a node or wants to have the blockchain data has to be prepared the right types of equipment and knowledge to handle all of the data. 

The witness nodes maintain the integrity of the BitShares Blockchain. Moreover, Active witness node validates BitShares transactions and produce "blocks" for the BitShares Blockchain.  "Blocks" are groups of transactions that have been validated. Also, each transaction is formed of many operations. Without a witness node, we do not have any "blocks" and "chains".

These are some BitShares Active Witness Duties:

- Be a reliable block producer
- Maintain a public seednode
- Publish accurately, frequently updated (check 1-2 times per hour), price feeds for the Smart Coins & Market Pegged Assets

#### Data and Memory Reduction for Nodes
As we discussed before, Nodes consume a significant amount of data. So, BitShares offers some plugins to manage the memory and the blockchain history data. You can launch a node with the witness plugin parameter(s).  

- [ElasticSearch Plugin](/forge/plugins/elastic_search_plugin.md#elasticsearch-plugin)
  - How to store account history data into an elasticsearch database.
- [Memory Reduction for Nodes](/forge/plugins/nodes_memory_reduction.md#memory-reduction-for-nodes)
  - Help to reduce RAM usage significantly by using witness_node executable options.

Besides the plugins, there is a [configuration file `config.ini`](../nodes_full_witness/full_nodes.md#configuration) in the data directory. You can set values to the parameters and adjust tracking history sizes or log file sizes.   In the default configuration file, most settings have been commented.  You might want to examine the configuration file parameters for your witness node. 


### Voting and a Witness Node
All BTS Holders can vote to witness(es) from the BitShares UI wallet. It's important to use your right to vote. However, if you would like to do so, you can cast your voting right to a proxy who represents your interests. 

- Read more... Accounts - [Voting information](../accounts/accounts/voting-bh.md#voting)

### Open Public Testnet

#### Use TESTNET if you are interested in being a node

If you are interested in being a node (block producing witness node), Testnet might be an excellent place to start. You can set up the witness_node and get an account to understand how the processes work. 

- [The Open Public Testnet information](../testnets/public_testnet_details.md#the-open-public-testnet-information)

***


