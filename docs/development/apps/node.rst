

.. _how-to-run-full-node:


Launch a Node
**************

.. contents:: Table of Contents
   :local:

--------------------

Launch a Node (first time)
================================

How to launch the full node::

    ./programs/witness_node/witness_node

It takes an optional –data-dir parameter to define a working and data directory to store the configuration, blockchain and local databases (defaults to `witness_node_data_dir`). Those will be automatically created with default settings if they don’t exist locally set.

|

---------------


Enable Remote Procedure Calls
================================

In order to allow RPC calls for blockchain operations you need to modify the following entry in the configuration file::

    rpc-endpoint = 0.0.0.0:8090

This will open the **port 8090 for global queries only**. Since the witness node only maintains the blockchain and (unless you are an actively block producing witness) no private keys are involved, it is safe to expose your witness to the internet.


* :ref:`How to Run and Use a Full Node <how-to-run-full-node2>`

Although you would get default parameters in your config file for better memory reduction, you might want to learn about the plugin.

* :ref:`Memory Reduction for Nodes: <memory-nodes>`

  - Help to reduce RAM usage significantly by using witness_node executable options 

.. note:: Find more information about Node, check :ref:`Node Tutorials <witness-node-guide-tutorials>`.

|

---------------


Restart the Witness Node
================================

When restarting the witness node, it may be required to append the ``--replay-blockchain`` parameter to regenerate the local (in-memory) blockchain state. 

.. tip:: If you want to close the Witness Node in a clean way, use ``Ctrl-C`` in Windows.



|

.. _bts-nodes-info:

---------------
	
Public Full Node Information 
================================

* `BitShares Public Full Nodes (apiConfig) <https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js>`_
* `Global BitShares Node Distribution Map <https://bitshares.eu/network/>`_
* `Seed Nodes information (application.cpp) <https://github.com/bitshares/bitshares-core/blob/master/libraries/app/application.cpp>`_ 


|

---------------

 
.. _api-node-wss-con:

API Node Secure Web Socket Connection
================================

.. Tip:: How can a Let's Encrypt certificate be used with an API node to provide a secure web socket (wss) connection.

  - This can be done with two steps

    1. Concatenate the fullchain.pem with the privkey.pem --  ``cat fullchain.pem privkey.pem > combined.pem```

    2. In the node's configuration file, point the TLS certificate to combined.pem, and leave the TLS certificate password as blank.

 
 
 

|

