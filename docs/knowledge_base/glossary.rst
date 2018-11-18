
.. _glossary-list:

******************************
Glossary
******************************


.. contents:: Table of Contents
   :local:
   
---------------

A 
-----
API node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- A (public) API node is a node that accept API calls via JSON RPC interface. It configured **RPC endpoint** parameter. With the API interface, clients can query data on the chain, can also broadcast transactions to the network.

|

--------------

B 
-----
 
BitShares Blockchain
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- "BitShares" --> "BitShares Blockchain"



Blockchain
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- The Blockchain serves as a journal of user-signed instructions that become a binding agreement as soon as they are included into a block. After inclusion into a block, the agreements are stored indefinitely by means of a hash-linked-list (the Blockchain).


Block Producer
^^^^^^^^^^^^^^^^^^^^^^^^^
- A witness get enough votes and became a block producer (a.k.a. active witness). 
 
  - Block Producer or Active Witness (belongs to a voted-in witness and is configured with correct key)
  


Witness Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- A witness node would be a full-history node, and each witness validates a transaction and broadcasts it. Witness nodes provide the complete history and preserve it as a failsafe. 
- Witnesses can be categorized into two groups that would be determined how much votes they received.   If a witness gets enough votes, the witness becomes an active witness and can produce block(s). Therefore, we call active witnesses "Block Producers".

  - **Block Producer**  (a.k.a. active witness node) belongs to a voted-in witness and is configured with correct key.  Block producers are validating signatures and timestamping transactions by including them in a block and broadcast it when they get their turn "time slot".  Each time, block producers produce a block, they get paid for their services.
  - Non-Block producer (a.k.a. standby witness node) is either a not-voted-in witness or a voted-in witness with a different key. If it gets enough votes then it's an active witness. 
  

BTS Holder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- "shareholder / stakeholders" --> "BTS Holder"

|

--------------

C  
-----

CLI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- Command-line interface

config.ini (in data-dir)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- To generate a default config file: First, *delete* the current directory defined by your ``—data-dir`` switch, then restart ``witness_node``. A new config.ini file will be generated in that directory. 

.. Note:: the above *delete* will also delete the ``/blockchain`` folder, so perhaps better to just locate the file fist, remove it and restart. Most of the parameters in the config have comments with descriptions and the default values

Consensus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- Consensus is the process by which a community comes to a universally recognized, unambiguous agreement on a piece of information. In the context of blockchains consensus means agreement about the validity rules for transactions, and the order in which they have been observed by the blockchain. This ultimately results in an agreement about the state of a database that is build deterministically from the those validity rules and the sequence of transactions.

|

--------------

D  
-----

Docker 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- Docker is meant for node operators. It makes it easier to deploy a new node and still allows configuration. I recommend mentioning it because docker is a term to most operators.  The Docker default config.ini is created when first launching the node. An example can be found in the /docker/ folder.

|

--------------

E 
-----

Elasticsearch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- Elasticsearch is a search engine based on Lucene library. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. (ref: `wiki <https://en.wikipedia.org/wiki/Elasticsearch>`_ ) 


ElasticSearch Plugin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- A "plugin" enables blockchain data to be stored in the elastic search database. A separate instruction guide exists in the -core/wiki. Once data is in ES database, it can be easily access through kibana (e.g. using kibana.bitshares.eu). Many libraries exist to read data from elastic search.

|

--------------

F 
-----

Faucet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- Faucet Role: The faucet address is used to pay the registration fee for new users.

Full Node
^^^^^^^^^^^^^^^^^^
- Basically, nodes are keeping all transactions (histories) that have occurred on the blockchain and maintaining the integrity of a blockchain. A full node is a full-history node that loaded account history via configuration. 


|

--------------

N 
-----

Native Core Token BTS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- BTS


Nodes 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* API Nodes (i.e., nodes with an open RPC port)
   
  - Nodes with Full History (a.k.a. full nodes)
  - Nodes with Partial History
  
* Seed Nodes (i.e., nodes with an open P2P port)
* Block Producing Nodes

  - Block producer or Active Witness Node (belongs to a voted-in witness and is configured with correct key)
  - Standby Witness Node (either a not-voted-in witness, or a voted-in witness with different key)



|

--------------

P 
-----

plugins
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- A plugin is a software component that adds a specific feature to an existing computer program. When a program supports plug-ins, it enables customization. (ref: `wiki <https://en.wikipedia.org/wiki/Plug-in_(computing)>`_ )

Price feed
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- Price feed providers use this operation to publish their price feeds for market-issued assets. A price feed is used to tune the market for a particular market-issued asset.

|

--------------

S 
-----

Seed Nodes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- Seed nodes are the first nodes of the network.It allows other nodes to get started by connecting to them.
- In BitShares, a seed node is a node that accept incoming P2P connection. Its address is hard coded in the program, so when a new node starts, it will connect to the seed nodes by default. Every node (including seed nodes) tells the connected nodes where other nodes are, so all nodes can connect to each other.

|

--------------

T 
-----
Token
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- "Asset" --> "Token" 

|

--------------

U 
-----
UI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- User Interface

Units
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- shares of an asset/token

unit test
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- In BitShares-Core unit test files locate in a /test/ folder. They are meant to ensure that the code behaves the right way.

|

--------------

W 
-----



Working Budget
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- "reserves or treasury" --> "Working Budget"


|

