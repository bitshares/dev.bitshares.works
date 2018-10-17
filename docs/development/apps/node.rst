

.. _how-to-run-full-node:

****************************
Full Nodes (Witness Nodes)
****************************

.. contents:: Table of Contents
   :local:

--------------------

Type of Witness nodes
==========================

* **Non-block producing** witness nodes
* **Block producing** witness nodes (more requirements and duties)

We distinguish between full nodes (a.k.a. non-block producing witness nodes) and block producing witness nodes. Both are implemented by the same executable but the latter requires some additional parameters to be defined and the corresponding witness voted active by the BTS Holders.

Both represent nodes in the network that verify all transactions and blocks against the current state of the overall network. Hence, we recommend all service providers to run and maintain their own full nodes for reliability and security reasons.


.. _system-requirements-node:

System Requirements
==========================

.. Attention:: **IMPORTANT:** By default the witness node will start in reduced memory mode by using some of the commands detailed in :ref:`Memory reduction for nodes <memory-nodes>`.
In order to run a full node with all the account history you need to remove ``partial-operations`` and ``max-ops-per-account`` from your config file. Please note that currently(2018-10-17) a full node will need more than 160GB of RAM to operate and required memory is growing fast. Consider the following table as minimal requirements before running a node:


+-------------------+--------------------+-----------------+-------------------+
|    Default        |    Full            |     Minimal     | ElasticSearch     |
+===================+====================+=================+===================+
| 100G SSD, 16G RAM | 200G SSD, 120G RAM | 80G SSD, 8G RAM | 500G SSD, 32G RAM |
+-------------------+--------------------+-----------------+-------------------+


Full Node
===============

How to launch the full node::

    ./programs/witness_node/witness_node

It takes an optional –data-dir parameter to define a working and data directory to store the configuration, blockchain and local databases (defaults to `witness_node_data_dir`). Those will be automatically created with default settings if they don’t exist locally set.


.. node-config-example:

.. include:: node_config_example.rst 


------------

Enable Remote Procedure Calls
================================

In order to allow RPC calls for blockchain operations you need to modify the following entry in the configuration file::

    rpc-endpoint = 0.0.0.0:8090

This will open the port 8090 for global queries only. Since the witness node only maintains the blockchain and (unless you are an actively block producing witness) no private keys are involved, it is safe to expose your witness to the internet.

Restart the Witness Node
=================================

When restarting the witness node, it may be required to append the ``--replay-blockchain`` parameter to regenerate the local (in-memory) blockchain state.

	
Public Full Node information 
==============================

* Here is the latest list of `BitShares Public Full Nodes (apiConfig) <https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js>`_


|

|

