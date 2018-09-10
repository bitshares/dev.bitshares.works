
.. _public-testnet-details:

*************************************
Quick Start Guide - Public Testnet
*************************************

The BitShares Open Public Testnets has been deployed and is fully functional for anyone to use and is shared among developers. All developers take the opportunity to construct a tutorial.


.. contents:: Table of Contents
   :local:
   
-------

Use the UI Wallet on Public Testnet
=======================================

- Go to a Web Wallet - Public testnet: (http://testnet.bitshares.eu/) 
- If you need to create an account, set the testnet faucet address
   - Faucet: (https://faucet.testnet.bitshares.eu/)
   - Go to [Setting] menu - [Faucet] and set the above Faucet address 
- Create an account
   - [CREATE ACCOUNT] button is for a CLOUD Wallet 
   - [advanced form] button is for a LOCAL Wallet
- Receive Testnet Cote asset *TEST*.
   - You will have `10k TEST` in your account for free upon registration.

.. note:: **Faucet role**: The faucet address is used to pay the registration fee for new users.

-------------------


Use the CLI on Public Testnet
=================================

In this section, we show you how to set up on **Ubuntu 16.04 LTS(64 bit)** environment as an example.

More options to see :ref:`Installation Guide <installation>`.

1. Install Dependencies::

	 sudo apt-get update
	 sudo apt-get install autoconf cmake git libboost-all-dev libssl-dev g++ libcurl4-openssl-dev

2. Build BitShares Core - Select **testnet** branch

*In order to run a full node, get the sources (branch testnet) and compile*::

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


