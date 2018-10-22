

.. _how-to-run-full-node2:

**********************************
How to Run and Use a Full Node
**********************************

.. contents:: Table of Contents
   :local:

--------------------

In order to improve decentralization of service, every user can run his own full node (often referred to non-block-producing witness node) and we here show how to do so.


Installation and build the Witness Node
=========================================

For the installation of the BitShares Core, please see the :ref:`Installation Guide <installation-guide>`:


To reduce compilation time, you can tell the compile infrastructure to only compile the witness node by running.::

	$ make witness_node

instead of::

	$ make

	

Run the Full Node
===============================

In order to run a full node that we can connect to, we need to open the RPC interface, this can be done by::

./programs/witness_node/witness_node --rpc-endpoint="0.0.0.0:8090"

This will open port 8090 and make it available over the internet (unless you run behind a router/firewall). 

If you want to open only your (local) machine, replace 0.0.0.0 by localhost (e.g., --rpc-endpoint="127.0.0.1:8090" ).


.. Note:: Full node needs to synchronize the blockchain with the network first, which might take a few hours or days.

	
------------------------------

|

.. _howto-connect-own-full-node-gui:

Connect to your own Full Node (GUI)
=======================================

In your wallet (may it be the light wallet or a hosted wallet) you can can define the full node to which is should connect to in the preferences:

1. Open Side Menu and selsct [**Settings**]
2. In the Settings, click **Nodes**
3. If **Automatically select node is ``ON``, click to ``OFF`` the switch.

.. image:: ../../_static/imgs/nodes-1.png
        :alt: BitShares 
        :width: 650px
        :align: center
		
There are four tabs. [AVAILABLE], [PERSONSL], [HIDDEN], and [TESTNET]. 

4. Click [**PERSONAL**] and [**ADD NODE**]
		
.. image:: ../../_static/imgs/nodes-2.png
        :alt: BitShares 
        :width: 650px
        :align: center
				
5. An Add Node form opens. Type, 

* **NAME**: (a node name )
* **ADDRESS**: (a node address)

For your own full nodes address, type::

   ws://localhost:8090

   
.. image:: ../../_static/imgs/nodes-3.png
        :alt: BitShares 
        :width: 600px
        :align: center
		
6. Click [**CONFIRM**].

Congratulation, you are now connected to the network via your own full node.




|

|

