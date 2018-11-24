
.. _public-testnet-details:

*************************************
Quick Start Guide - Public Testnet
*************************************

The BitShares Open Public Testnet has been deployed and is fully functional for anyone to use and is shared among developers. All developers take the opportunity to construct a tutorial.


.. contents:: Table of Contents
   :local:
   
-------

Use a UI Wallet on Public Testnet
=======================================

- Go to a Web Wallet - Public testnet: (http://testnet.bitshares.eu/) 
- If you need to create an account, set the testnet faucet address
   - Faucet: (https://faucet.testnet.bitshares.eu/)
   - Go to [Setting] menu - [Faucet] and set the above Faucet address 
- Create an account
   - For a CLOUD Wallet: Use a [CREATE ACCOUNT] button
   - For a LOCAL Wallet: Use an [advanced form] button
- Receive Testnet Core asset *TEST*.
   - You will have `10k TEST` in your account for free upon registration.

.. note:: **Faucet Role**: The faucet address is used to pay the registration fee for new users.

-------------------


Use a CLI on Public Testnet
=================================

In this section, we show you how to set up on **Ubuntu 16.04 LTS(64 bit)** environment as an example.

To see more options: Go to :ref:`Installation Guide <installation-guide>`.

1. Install Dependencies::

	 sudo apt-get update
	 sudo apt-get install autoconf cmake git libboost-all-dev libssl-dev g++ libcurl4-openssl-dev

2. Build BitShares Core - Select **testnet** branch

*In order to run a full node, get the sources (branch* **testnet** *) and compile*::

	 git clone https://github.com/bitshares/bitshares-core.git bitshares-core-testnet
	 cd bitshares-core-testnet    
	 git checkout testnet
	 cmake

3. After compile, run the node with::

        $ programs/witness_node/witness_node --genesis-json=genesis.json


.. Warning:: On this network, **everything** is allowed. As a consequence, the outside price of any token on this blockchain is exactly **zero**. A reset of the blockchain may occur at any moment!


-----------------

Other References
---------------------

- `Testnet - Python Scripts <https://github.com/BitSharesEurope/testnet-pythonscripts>`_

- **Seed node**: `testnet.bitshares.eu:11010`
- Chain-id: `39f5e2ede1f8bc1a3a54a7914414e3779e33193f1f5693510e73cb7a87617447`
- `Genesis Block - for Testnet <https://github.com/bitshares/bitshares-core/blob/testnet/genesis.json>`_


|

|


