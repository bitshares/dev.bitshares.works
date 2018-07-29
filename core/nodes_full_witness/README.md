
#### Table of Contents:
- [BitShares Nodes](/core/nodes_full_witness/README.md#bitshares-nodes-and-p2p-network)
- [Full Node](/core/nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)
- [Witness Node](/core/nodes_full_witness/active_witness.md#become-an-active-witness)
- FAQ
  - [Committee](/core/tutorials/FAQ.md#committee)
  - [Witness](/core/tutorials/FAQ.md#witness)

***

After you installed BitShares Core successfully, you have `witness_node` and `cli_wallet`. In this first section, we talk about the BitShaares Nodes and some expectations.

## BitShares Nodes and P2P network
- [BitShares Client and Node](#bitshares-client-and-node)
- [Witness Node and Full Node](#witness-node-and-full-node)
- [Witness Node's Roles and Duties](#witness-nodes-roles-and-duties)
- [Voting and a Witness Node](#voting-and-a-witness-node)
- [Open Public Testnet](#open-public-testnet)


#### What is the Nodes?
What's a node? According to Wikipedia, in Computer Science, 

 > A node is a basic unit used in computer science. Nodes are devices or data points on a more extensive network. Devices such as a personal computer, cell phone, or printer are nodes. When defining nodes on the internet, a node is anything that has an IP address.  
From <https://en.wikipedia.org/wiki/Node_(computer_science)> 

### BitShares Client and Node

The BitShares client uses a peer-to-peer network. Each client connects to the BitShares Full Node to interact with the network to query the data and broadcast messages/transactions directly. If you are using a command prompt screen, you might try some API calls to query BitShares Blockchain data after you connect to a full node. 

### Witness Node and Full Node  

When we talk about the BitShares Nodes, we use **witness node** and **full node** terms almost interchangeably.  However, there are some different roles between them. The "witness node" works as a **block producer**. The "full node" works as a **non-block producer** and mainly provides an API service which interacts with BitShares clients and applications and queries the BitShares Blockchain data. 

- [Types of nodes](/core/nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)

Both are implemented by the executable (*witness_node*), but in the command lines extra parameters can represent differences each node operation.  For example, a (block producing) witness node has been provided an authorized block-signing private key, on the other hand, a (non-block producing) full node is not.  However, both *interact* with the decentralized network (blockchain). 

#### BitShares Public Full Nodes
If you do not have own node to run or connect, you can use one of the BitShares Public API services (public full nodes) to interact with the BitShares blockchain. 

- The latest list of [BitShares Public Full Nodes](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information.

### Witness Node's Roles and Duties

The witness node has a copy of the full BitShares blockchain data (transaction history) on a computer or other hardware devices.  

Anyone who wishes to have own copy of the blockchain data can do so. However, that would take hours (days) to download and sync the data and require many memories to run the blockchain (*Check here [BitShares updated System Requirements](/core/nodes_full_witness/full_nodes.md#system-requirements)*). Therefore, anyone who is interested in being a node or wants to have the blockchain data has to be prepared the right types of equipment and knowledge to handle all of the data. 

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

Besides the plugins, there is a [configuration file `config.ini`](/core/nodes_full_witness/full_nodes.md#configuration) in the data directory. You can set values to the parameters and adjust tracking history sizes or log file sizes.   In the default configuration file, most settings have been commented.  You might want to examine the configuration file parameters for your witness node. 


### Voting and a Witness Node
All BTS Holders can vote to witness(es) from the BitShares UI wallet. It's important to use your right to vote. However, if you would like to do so, you can cast your voting right to a proxy who represents your interests. 

- Read more... Accounts - [Voting information](/core/accounts/accounts/voting-bh.md#voting)

### Open Public Testnet

#### Use TESTNET if you are interested in being a node

If you are interested in being a node (block producing witness node), Testnet might be an excellent place to start. You can set up the witness_node and get an account to understand how the processes work. 

- [The Open Public Testnet information](/core/testnets/public_testnet_details.md#the-open-public-testnet-information)

***


## Full Node

- [Running a BitShares API Node](/core/nodes_full_witness/running-api-node.md#running-a-bitshares-api-node)   


### [Full Nodes (Witness Nodes)](/core/nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)
- [Type of Witness nodes](/core/nodes_full_witness/full_nodes.md#type-of-witness-nodes)
   - Non-Block producing
   - Block producing
- [System Requirements](/core/nodes_full_witness/full_nodes.md#system-requirements)      
- [How to launch the full node](/core/nodes_full_witness/full_nodes.md#how-to-launch-the-full-node)
- [Configuration - `config.ini`](/core/nodes_full_witness/full_nodes.md#configuration)
- [Enabling Remote Procedure Calls (RPC)](/core/nodes_full_witness/full_nodes.md#enabling-remote-procedure-calls-rpc)
- [Restarting the Witness Node](/core/nodes_full_witness/full_nodes.md#restarting-the-witness-node)
- [Public Full Node information](/core/nodes_full_witness/full_nodes.md#public-full-node-information)

***

## Witness Node

### [Become an Active Witness](/core/nodes_full_witness/active_witness.md#become-an-active-witness)

- [A Block-Producing Witness](/core/nodes_full_witness/active_witness.md#a-block-producing-witness)
   - Requirements
   - Technical Specifications
   - Active Witness Duties
 - [How to become a Block-Producing Witness](/core/nodes_full_witness/active_witness.md#how-to-become-a-block-producing-witness)
   - 1.[Run the Witness/Full Node on the Network](/core/nodes_full_witness/active_witness.md#1-run-the-witnessfull-node-on-the-network)
   - 2.[Create a CLI Wallet](/core/nodes_full_witness/active_witness.md#2-create-a-cli-wallet)
   - 3.[Import your Account (and funds) into CLI Wallet](/core/nodes_full_witness/active_witness.md#3-import-your-account-and-funds-into-cli-wallet)
   - 4.[Upgrade your Account to a Lifetime Member](/core/nodes_full_witness/active_witness.md#4-upgrade-your-account-to-a-lifetime-member)
   - 5.[Register a New Witness](/core/nodes_full_witness/active_witness.md#5-registering-a-new-witness)
   - 6.[Upvote the Witnes](/core/nodes_full_witness/active_witness.md#6-upvote-the-witness)
   - 7.[Configuration of the Witness Node](/core/nodes_full_witness/active_witness.md#7-configuration-of-the-witness-node)
   - 8.[Verifying Block Production](/core/nodes_full_witness/active_witness.md#8-verifying-block-production)
- [Backup Server](/core/nodes_full_witness/active_witness.md#backup-server)
- [Price Feeds](/core/nodes_full_witness/active_witness.md#price-feeds)

***

### FAQ - Committee

- [How to Create a new Committee Member](/core/tutorials/committee_howto.md#how-to-creating-a-new-committee-member)
- [more... ](/core/tutorials/FAQ.md#committee)

### FAQ - Witness 
- [How to Change the Signing Key of your Witness?](/core/tutorials/FAQ.md#q-how-to-change-the-signing-key-of-your-witness)
- [more...](/core/tutorials/FAQ.md#witness)

***
