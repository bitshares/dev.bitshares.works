
.. _secure-network-configuration:

Secure Network and Wallet Configuration 
===============================================


.. contents:: Table of Contents
   :local:
   
-------

For high security, we provide a so called **delayed** full node which accepts the parameter `trusted-node` for an RPC endpoint of a trusted validating node. The trusted-node is a regular full node directly connected to the P2P network that works as a proxy. The delayed node will delay blocks until they are **irreversible**. Depending on the block interval and the number of witnesses, this may lead to a few minutes of delay.

Set up
----------------

.. image:: ../../../../_images/secure-setup.png
        :alt: BitShares Architecture
        :width: 650px
        :align: center
		

.. Note:: The delayed full node should be in the same *local* network as the trusted full node is in the same network and has internet access.

Hence we will work with the following IPs and open the corresponding RPC ports

* Trusted Full Node:

  - extern: internet access **required**
  - intern: `192.168.0.100`
  - port: `8090`
   
* Delayed Full Node:

  - extern: **no** internet access required
  - intern: `192.168.0.101`
  - port: `8090`
   
* Wallet:

  - extern: **no** internet access required
  - intern: `192.168.0.102`
  - port: `8092`

   
Trusted Full Node
------------------------

The trusted full node is your entry point to the BitShares P2P network. It will hold the blockchain, connect to other peers, and will receive new blocks in real-time.

::

    ./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"
 
.. Note:: A _witness_ node is identical to a full node if no authorized block-signing private key is provided.


Delayed Full Node
------------------------

The delayed full node will provide us with a delayed and several times confirmed and verified blockchain. Even though DPOS is more resistant against forks than most other blockchain consensus schemes, we delay the blockchain here to reduces the risk of forks even more. In the end, the delayed full node is supposed to never enter an invalid fork. All transactions that are confirmed by the delayed node are irreversible.

The delayed full node will need the IP address and port of the p2p-endpoint from the trusted full node. We also need to open the RPC/Websocket port (to the local network!) so that we can interface using RPC-JSON calls.

::

    ./programs/witness_node/witness_node --trusted-node="192.168.0.100:8090" \
                                        --rpc-endpoint="192.168.0.101:8090"
                                        -s "0.0.0.0:0" \
                                        --p2p-endpoint="0.0.0.0:0" \
                                        --seed-nodes "[]"

We could now connect via RPC:

- ``192.168.0.100:8090`` : The trusted full node exposed to the internet
- ``192.168.0.101:8090`` : The delayed full node not exposed to the internet

.. Note:: For security reasons, an exchange should only interface with the delayed full node.

For obvious reasons, the trusted full node is should be running before attempting to start the delayed full node.

**For customer deposits, we will interface to the delayed node’s API using 192.168.0.101:8090.**
 
 
Wallet
------------

The wallet initiates outgoing transfers and has to connect to your trusted node because the _delayed_ node has no P2P connections. We furthermore open up a **RPC-JSON-HTTP** port to be able to interface with API requests. The wallet can be connected to the trusted node and listens for RPC requests on port `8092` with:

::

    ./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                     --rpc-http-endpoint="192.168.0.102:8092"

.. Note:: For security reasons, the wallet should only listen to localhost or the local network and should **NEVER** be exposed to the internet.

**For customer withdrawals, we will interface to the wallet’s API using 192.168.0.102:8092.**



|

