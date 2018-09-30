
.. _memory-nodes:

***************************
Memory Reduction for Nodes 
***************************


.. contents:: Table of Contents
   :local:
   
-------

Four New Options to Reduce RAM
=========================================

programs/witness_node/witness_node --help
------------------------------------------------

:--plugins arg:  Space-separated list of plugins to activate 

Options for plugin accout_history
----------------------------------------

:--track-account arg:  Account ID to track history for (may specify multiple times) 
:--max-ops-per-account arg: Maximum number of operations per account will be kept in memory 
:--partial-operations arg: Keep only those operations in memory that are related to account history tracking 

--------------

--plugins
--------------

Allows to run only the plugins you want.

By default, if the plugin parameter is not present in the command line startup the node will start with the default plugins loaded, these are:

- witness
- account_history
- market_history

You can launch a node only with the witness plugin activated like the following if you are after just validating blocks::

    programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                   --rpc-endpoint "127.0.0.1:8090" 
									   --plugins "witness"


--track-account
----------------------------------

Allows to track only the history of selected accounts.

Suppose you have an application wallet or something that is only interested in the history of one account or just a few accounts, no need to spend the memory in the huge amount of account history from the rest of the network. You will still be able to do transfers and everything as normal, it is just the account history that will not be available.

In order to track the history only for just one account you may start the node as::

    programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                   --rpc-endpoint "127.0.0.1:8090" 
									   --track-account "\"1.2.282\""


To track multiple accounts::

    programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                   --rpc-endpoint "127.0.0.1:8090" 
									   --track-account "\"1.2.282\"" "\"1.2.24484\"" "\"1.2.2058\""

--max-ops-per-account
----------------------------------

Add a number of operations the node will store for each account in the network.

We found out that most of the blockchain size is actually history of millions of orders made by automated bot accounts in the system. The market making these accounts do, is very needed for the DEX liquidity, but is of little value for most nodes.

This parameter allows to go deleting the oldest operation history from all accounts. Balances and everything will still be fine, please remember this is *only deleting history*.

By limiting the number of operations per account to 1000 the blockchain decrease in size is more than notorious and will allow you to run nodes in reduced memory machines, can run with 4-5 gigs by using combinations around this option.

Reduce the number of operations for each account that the node will save in the blockchain to 100 by starting with::

    programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                   --rpc-endpoint "127.0.0.1:8090" 
									   --max-ops-per-account 100
 
--partial-operations
----------------------------------

Remove operation history objects.

BitShares-core stores operations in two different objects, the 2.9.X and the 1.11.X. they are not exactly the same but if you are removing ops with `--track-account` or `--max-ops-per-account` it makes sense that you also add this option to reduce memory usage even more.::

    programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                   --rpc-endpoint "127.0.0.1:8090" 
									   --max-ops-per-account 100 
									   --partial-operations true
 

-------------

Combinations
===========================

Combinations that make sense are all valid and can be used to suit your needs.

I personally start my nodes with 1000 ops per account and partial operations::

    programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                   --rpc-endpoint "127.0.0.1:8090" 
									   --max-ops-per-account 1000 
									   --partial-operations true
   

This will allow me to run the node with less than 5 gigs(4.820492G)::

    ffffffffff600000      4K r-x--   [ anon ]
     total          4820492K
    root@alfredo:~# pmap 28685

Special Notes
---------------------

- A new option could be `untrack-account`. we could identify the biggers and run a node with the account history of bots out.

-------------
  
|
  
Contributor: @oxarbitrage
 
 
|

|

