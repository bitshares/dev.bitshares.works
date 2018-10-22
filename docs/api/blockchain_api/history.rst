


Account History API
***********************

The history API is available from the full node via websockets.

If you have not set up your websockets connection, please read :doc:`this
article <websocket>`.

.. contents:: Table of Contents
   :local:
   
----------------
   

Account History
=======================
get_account_history
-----------------------------
.. doxygenfunction:: graphene::app::history_api::get_account_history

get_account_history_operations
-----------------------------
.. doxygenfunction:: graphene::app::history_api::get_account_history_operations

get_relative_account_history
-----------------------------
.. doxygenfunction:: graphene::app::history_api::get_relative_account_history


|

**************

Market History
==================
get_fill_order_history
-------------------------------
.. doxygenfunction:: graphene::app::history_api::get_fill_order_history

get_market_history
------------------------------
.. doxygenfunction:: graphene::app::history_api::get_market_history

get_market_history_buckets
------------------------------------
.. doxygenfunction:: graphene::app::history_api::get_market_history_buckets

|
