
.. _private-testnet-genesis-example:

Private Testnet Genesis file 
===================================

As you know, each blockchain starts with a genesis block that definitions are in a genesis.json file. **The bitshares-core-testnet has already prepared own genesis.jsom file for the Public Testnet.**  You will not need to modify the genesis.json file. You will find the ``genesis.json`` in the **bitshares-core-testnet** directory.

If you want to test the feature set of current mainnet without connecting to mainnet, you must start a new (private) network.  

In a private testnet, you have to create and configure own genesis file by creating a subdirectory named genesis and create a file within it named my-genesis.json. This file dictates the initial state of the network.  You must put an account information into the genesis file with keys that you have generated yourself. Before you start a ``witness_node``, you must set your my-genesis.json file name into a data configuration file ``config.ini``.

If you want to generate new keys paies, you can use ``suggest-brain-key`` option in cli_wallet.




