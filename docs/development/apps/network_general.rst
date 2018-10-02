

General Network and Wallet Configuration 
=============================================

.. contents:: Table of Contents
   :local:
   
-------

For general purpose setups, we recommend a reduced complexity setup that looks as follows.

Set up
-----------

.. image:: ../../../../_static/structures/general-setup.png
        :alt: BitShares Architecture
        :width: 650px
        :align: center
		


**We will work with the following IPs and open the corresponding RPC ports:**


* Trusted Full Node:

  - extern: internet access **required**
  - intern: `192.168.0.100`
  - port: `8090`
   
* Wallet:

  - extern: **no** internet access required
  - intern: `192.168.0.102`
  - port: `8092`

	  
Trusted Full Node
----------------------

The trusted full node is your entry point to the BitShares P2P network. It will hold the blockchain, connect to other peers, and will receive new blocks in real-time.

For the trusted full node, the default settings can be used. For later, we will need to open the RPC port and listen to an IP address to connect the wallet to.

::

    ./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"


.. Note:: A witness node is identical to a full node if no authorized block-signing private key is provided.


Wallet
----------------

We open up a ``RPC-JSON-HTTP`` port to be able to interface with API requests. The wallet can be connected to the trusted node and listens for RPC requests on port ``8092`` with:

::

    ./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                    --rpc-http-endpoint="192.168.0.102:8092"

.. Note:: For security reasons, the wallet should only listen to localhost or the local network and should **NEVER** be exposed to the internet.

**For customer withdrawals, we will interface to the wallet’s API using 192.168.0.102:8092.**

--------------------------


|

