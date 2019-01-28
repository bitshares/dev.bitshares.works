
.. _public-testnet-genesis-example:

Public Testnet Genesis file 
===================================

As you know, each blockchain starts with a genesis block that definitions are in a genesis.json file. **The bitshares-core-testnet has already prepared own genesis.jsom file for the Public Testnet.**  You will not need to modify the genesis.json file. You will find the ``genesis.json`` in the **bitshares-core-testnet** directory.

If you want to test the feature set of current mainnet without connecting to mainnet, you must start a new (private) network.  

In a private testnet, you have to create and configure own genesis file by using ``--create-genesis-json`` (e.g., ``./witness_node --create-genesis-json=my-genesis.json``). And you must put an account into the genesis file with keys that you have generated yourself. Before you start a ``witness_node``, you must set your my-genesis.json file in a data configuration file ``config.ini``.



genesis.json for a testnet
--------------------------


::

	{
 
	}
