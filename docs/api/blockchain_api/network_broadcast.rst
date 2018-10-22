

Network Broadcast API
***********************

The network broadcast API is available from the full node via websockets.

If you have not set up your websockets connection, please read :doc:`this
article <websocket>`.

.. contents:: Table of Contents
   :local:
   
----------------

Transactions
================
broadcast_transaction
--------------------------
.. doxygenfunction:: graphene::app::network_broadcast_api::broadcast_transaction

broadcast_transaction_with_callback
----------------------------------------
.. doxygenfunction:: graphene::app::network_broadcast_api::broadcast_transaction_with_callback

|

**************

Block
============
broadcast_block
---------------------------
.. doxygenfunction:: graphene::app::network_broadcast_api::broadcast_block
