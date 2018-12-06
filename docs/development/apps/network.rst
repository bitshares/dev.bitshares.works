
.. _network-setups:

*****************************************
Network and Wallet Configuration 
*****************************************

.. contents:: Table of Contents
    :depth: 2

--------------
	
Components
========================

P2P Network
------------

The BitShares client uses a peer-to-peer network to connect and broadcasts transactions there. A block producing full node will eventually catch your transcaction and validates it by adding it into a new block.

Trusted Full Node
------------------

We will use a Full node to connect to the network directly. We call it trusted since it is supposed to be under our control.

Wallet
--------------

The wallet is used to initiate transfers (customer withdrawals) and connects to the trusted full node.

Wallet API
--------------

Since we have a delayed full node that we can fully trust, we will interface with this node to query the blockchain and receive notifications from it one balance changes. Hence, we use this API to watch deposits of users into the exchange’s account. Because the delayed node only knows about irreversible blocks all transactions are at this point irreversible as well. For customer withdrawals, we will interface with the wallet to initiate transfers to the accounts of the customers on request. As we are connected to the trusted node directly, there will not be any delay on withdrawals.





|

|

