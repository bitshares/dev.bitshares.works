
.. _public-testnet-genesis-example:

Public Testnet Genesis file 
===================================

As you know, each blockchain starts with a genesis block that definitions are in a genesis file. The bitshares-core-testnet has already prepared **a genesis.json file for the Public Testnet.**  Unless you want to adjust parameter values, you will not need to modify the public testnet genesis.json file. 


If you want to test the feature set of current mainnet without connecting to mainnet, you must start a new (**private**) network.  


.. Attention:: ``--create-genesis-json`` option will be removed. In a pprivate testnet, you need to create a file named ``my-genesis.json``. You can use the existing public testnet genesis.json file as an example. That file can be found at https://github.com/bitshares/bitshares-core/blob/testnet/genesis.json



genesis.json for a testnet
--------------------------


::

	{
 
	}
