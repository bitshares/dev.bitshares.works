
#### Table of Contents:

**TODO: Update these links, I'm not sure the proper syntax**
- [BitShares Network Components](TBD)
  - [Peer-to-Peer Network](TBD)
  - [Validation Nodes](TBD)
    - [Seed Node](TBD)
    - [API Node](TBD)
    - [Full Node](TBD)
    - [Local Node](TBD)
    - [Block Producing Node](TBD)
  - [Client Wallets](TBD)
    - [CLI Wallet](TBD)
    - [Web Wallet](TBD)
    - [Light Wallet](TBD)
- [BitShares Roles](TBD)
  - [Seed Node Operators](TBD)
  - [Public API Node Operators](TBD)
  - [Block Producers](TBD)

**TODO: Perhaps we want place these topics in a different section? It seems maybe the Roles section above is separate from the Components.**
- [Block Producer Roles and Duties](TBD)
- [Block Producer Voting](TBD)
- [Open Public Testnet](#open-public-testnet)

***

**TODO: deprecate the use of the term "witness" as I feel "block producer" is a better term going forward. It's a battle I may not win in the short term, but long term I feel it's a good goal.**

**TODO: Consider removing this block, as it may not be needed in this context**
#### What is a Node?
What's a node? According to Wikipedia, in Computer Science, 

 > A node is a basic unit used in computer science. Nodes are devices or data points on a more extensive network. Devices such as a personal computer, cell phone, or printer are nodes. When defining nodes on the internet, a node is anything that has an IP address.  
From <https://en.wikipedia.org/wiki/Node_(computer_science)> 

### BitShares Network Cocmponents

The BitShares Network is comprosed of three components: a peer-to-peer network, client wallets and validation nodes. As an end user, your interface to the blockchain is thru a client wallet. However, the client must connect to a server to interact with the blockchain. BitShares-Core provides a command line interface `cli_wallet` and BitShares-UI provides a graphical user interface wallet either thru a web browser [wallet.bitshares.org](https://wallet.bitshares.org) or desktop light wallet. These client wallets connect to a special type of validation node called an API server, which respond to client requests such as broadcasting a transaction or querying blockchain data. Transactions are broadcast by the API servers to other validation nodes using a peer-to-peer network, allowing each to observe, validate and store the same data locally. Another special type of validation node is the block producing node. These are operated by the active block producers and have the authority to include valid transactions into new blocks. 

### Peer-to-Peer Network
**TODO: Introduce the concepts of netowrking, nodes, connections, peer-to-peer, message exchange, etc.**

### Validation Nodes
**TODO: Explain that all nodes are validation nodes on the P2P network. The differencec is in what data they retain and make available to connected peers or clients.**
When we talk about the BitShares Nodes, we use **witness node** and **full node** terms almost interchangeably.  However, there are some different roles between them. The "witness node" works as a **block producer**. The "full node" works as a **non-block producer** and mainly provides an API service which interacts with BitShares clients and applications and queries the BitShares Blockchain data. 

- [Types of nodes](../nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)

Both are implemented by the executable (*witness_node*), but in the command lines extra parameters can represent differences each node operation.  For example, a (block producing) witness node has been provided an authorized block-signing private key, on the other hand, a (non-block producing) full node is not.  However, both *interact* with the decentralized network (blockchain). 

#### Seed Node
**TODO: First node a new node on the P2P connects to. This is a full node of historical data, but need not retain market history or user data beyond validation. Clients do not connect to these, only other nodes**

#### API Node
**TODO: Expand on overview above. These must have market history and user history for the client to make use of.**
You can use one of the BitShares Public API Nodes to interact with the BitShares blockchain. 

- The latest list of [BitShares Public API Nodes](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information.

#### Full Node
**TODO: Talk about full nodes in context of what they contain**

#### Local Node
**TODO: Talk about an end user configuring their own personal node to connect a client to locally. They may configure it to track only their account to save memory resources.**

#### Block Producing Node
**TODO: Expand on overview. These need not maintain market history nor user history as no client will connect to them. They are not exposed to the Internet directly and relay through trusted peers for security** 

### Block Producer Role and Duties

Each validation node has a replica of the full BitShares blockchain data (transaction history) on their server or other hardware device. Anyone who wishes to maintain a replica of the blockchain data may do so by runing the `witness_node` software and allowing it to syncronize over the peer-to-peer network. However, this may take several hours to download the data and requires storage and memory capacities beyond a typical desktop workstation (*Check here [BitShares updated System Requirements](../nodes_full_witness/full_nodes.md#system-requirements)*). Therefore, anyone who is interested in running a node or wants to have the blockchain data has to be prepared with the correct type of equipment and knowledge to handle all of the data. 

**TODO: Continue edits from here on**
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


## Full Node

- [Running a BitShares API Node](../nodes_full_witness/running-api-node.md#running-a-bitshares-api-node)   


### [Full Nodes (Witness Nodes)](../nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)
- [Type of Witness nodes](../nodes_full_witness/full_nodes.md#type-of-witness-nodes)
   - Non-Block producing
   - Block producing
- [System Requirements](../nodes_full_witness/full_nodes.md#system-requirements)      
- [How to launch the full node](../nodes_full_witness/full_nodes.md#how-to-launch-the-full-node)
- [Configuration - `config.ini`](../nodes_full_witness/full_nodes.md#configuration)
- [Enabling Remote Procedure Calls (RPC)](../nodes_full_witness/full_nodes.md#enabling-remote-procedure-calls-rpc)
- [Restarting the Witness Node](../nodes_full_witness/full_nodes.md#restarting-the-witness-node)
- [Public Full Node information](../nodes_full_witness/full_nodes.md#public-full-node-information)

***

## Witness Node

### [Become an Active Witness](../nodes_full_witness/active_witness.md#become-an-active-witness)

- [A Block-Producing Witness](../nodes_full_witness/active_witness.md#a-block-producing-witness)
   - Requirements
   - Technical Specifications
   - Active Witness Duties
 - [How to become a Block-Producing Witness](../nodes_full_witness/active_witness.md#how-to-become-a-block-producing-witness)
   - 1.[Run the Witness/Full Node on the Network](../nodes_full_witness/active_witness.md#1-run-the-witnessfull-node-on-the-network)
   - 2.[Create a CLI Wallet](../nodes_full_witness/active_witness.md#2-create-a-cli-wallet)
   - 3.[Import your Account (and funds) into CLI Wallet](../nodes_full_witness/active_witness.md#3-import-your-account-and-funds-into-cli-wallet)
   - 4.[Upgrade your Account to a Lifetime Member](../nodes_full_witness/active_witness.md#4-upgrade-your-account-to-a-lifetime-member)
   - 5.[Register a New Witness](../nodes_full_witness/active_witness.md#5-registering-a-new-witness)
   - 6.[Upvote the Witnes](../nodes_full_witness/active_witness.md#6-upvote-the-witness)
   - 7.[Configuration of the Witness Node](../nodes_full_witness/active_witness.md#7-configuration-of-the-witness-node)
   - 8.[Verifying Block Production](../nodes_full_witness/active_witness.md#8-verifying-block-production)
- [Backup Server](../nodes_full_witness/active_witness.md#backup-server)
- [Price Feeds](../nodes_full_witness/active_witness.md#price-feeds)

***

### FAQ - Committee

- [How to Create a new Committee Member](../tutorials/committee_howto.md#how-to-creating-a-new-committee-member)
- [more... ](../tutorials/FAQ.md#committee)

### FAQ - Witness 
- [How to Change the Signing Key of your Witness?](../tutorials/FAQ.md#q-how-to-change-the-signing-key-of-your-witness)
- [more...](../tutorials/FAQ.md#witness)

***
