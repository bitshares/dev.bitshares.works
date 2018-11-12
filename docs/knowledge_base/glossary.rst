
.. _glossary-list:

******************************
Glossary
******************************


.. contents:: Table of Contents
   :local:
   
---------------

 
BitShares Blockchain
----------------------
- BitShares



Blockchain
-----------------
- The Blockchain serves as a journal of user-signed instructions that become a binding agreement as soon as they are included into a block. After inclusion into a block, the agreements are stored indefinitely by means of a hash-linked-list (the Blockchain).



BTS Holder
----------------------
- shareholder / stakeholders

CLI
----------------------
- Command-line interface

config.ini (in data-dir)
------------------------------------------------------------------
- To generate a default config file: First, *delete* the current directory defined by your ``—data-dir`` switch, then restart ``witness_node``. A new config.ini file will be generated in that directory. 

.. Note:: the above *delete* will also delete the ``/blockchain`` folder, so perhaps better to just locate the file fist, remove it and restart. Most of the parameters in the config have comments with descriptions and the default values

Consensus
----------------------
- Consensus is the process by which a community comes to a universally recognized, unambiguous agreement on a piece of information. In the context of blockchains consensus means agreement about the validity rules for transactions, and the order in which they have been observed by the blockchain. This ultimately results in an agreement about the state of a database that is build deterministically from the those validity rules and the sequence of transactions.

Docker 
----------------------
- Docker is meant for node operators. It makes it easier to deploy a new node and still allows configuration. I recommend mentioning it because docker is a term to most operators.  The Docker default config.ini is created when first launching the node. An example can be found in the /docker/ folder.


Elasticsearch
-------------------
- Elasticsearch is a search engine based on Lucene library. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. (ref: `wiki <https://en.wikipedia.org/wiki/Elasticsearch>`_ ) 


ElasticSearch Plugin
----------------------
- A "plugin" enables blockchain data to be stored in the elastic search database. A separate instruction guide exists in the -core/wiki. Once data is in ES database, it can be easily access through kibana (e.g. using kibana.bitshares.eu). Many libraries exist to read data from elastic search.

Faucet
---------------
- Faucet Role: The faucet address is used to pay the registration fee for new users.

Native Core Token BTS
----------------------
- BTS


Nodes (full node)
----------------------
- 


Nodes (witness node)
----------------------
- (Block Producer)



plugins
----------------------
- A plugin is a software component that adds a specific feature to an existing computer program. When a program supports plug-ins, it enables customization. (ref: `wiki <https://en.wikipedia.org/wiki/Plug-in_(computing)>`_ )

Price feed
----------------------
- Price feed providers use this operation to publish their price feeds for market-issued assets. A price feed is used to tune the market for a particular market-issued asset.


Seed Nodes
----------------------
- Seed nodes are the first nodes of the network.It allows other nodes to get started by connecting to them.


Token
----------------------
- Asset

UI
--------------
- User Interface


Units
----------------------
- shares of an asset/token

unit test
----------------------
- In BitShares-Core unit test files locate in a /test/ folder. They are meant to ensure that the code behaves the right way.


Witness Nodes
-------------------------
- Block Producing Node. In BitShares, witnesses serve a similar role of validating signatures and timestamping transactions by including them in blocks.


Working Budget
----------------------
- reserves or treasury
