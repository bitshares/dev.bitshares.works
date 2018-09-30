
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

configuration file (config.ini) - in data dir
------------------------------------------------------------------
- To generate a default config file: delete the current directory defined by your —data-dir switch, then restart witness_node. A new config.ini file will be generated in that directory. Note: the delete will also delete the /blockchain folder, so perhaps better to just locate the file fist, remove it and restart. Most of the parameters in the config have comments with descriptions and the default values

Consensus
----------------------
- Consensus is the process by which a community comes to a universally recognized, unambiguous agreement on a piece of information. In the context of blockchains consensus means agreement about the validity rules for transactions, and the order in which they have been observed by the blockchain. This ultimately results in an agreement about the state of a database that is build deterministically from the those validity rules and the sequence of transactions.

Docker 
----------------------
- Docker is meant for node operators. It makes it easier to deploy a new node and still allows configuration. I recommend mentioning it because docker is a term to most operators.  The Docker default config.ini is created when first launching the node. An example can be found in the /docker/ folder.


ElasticSearch Plugin
----------------------
- A "plugin" enables blockchain data to be stored in the elastic search database. A separate instruction guide exists in the -core/wiki. Once data is in ES database, it can be easily access through kibana (e.g. using kibana.bitshares.eu). Many libraries exist to read data from elastic search.

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
- 

Seed Nodes
----------------------
- Seed nodes are the first nodes of the network.It allows other nodes to get started by connecting to them.


Token
----------------------
- Asset


Units
----------------------
- shares of an asset/token

unit test
----------------------
- In BitShares-Core unit test files locate in a /test/ folder. They are meant to ensure that the code behaves the right way.





Working Budget
----------------------
- reserves or treasury
