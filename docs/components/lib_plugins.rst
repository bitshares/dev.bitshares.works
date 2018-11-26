
.. _lib-plugins:

*******************************************
Plugins
*******************************************

BitShares has prepared several plugins. The below list shows current BitShares plugins categories and plugin's names. You can find more information in BitShartes Github - `BitShares Plugins ReadMe <https://github.com/bitshares/bitshares-core/blob/master/libraries/plugins/README.md>`_.

  
A plugin adds a specific feature to an existing program. In BitShares, **witness, account_history, market_history, and grouped_orders plugins are available as a default**. However, you should specify plugin(s) for your purpose to run a node. You can define specific plugin(s) to activate when you start the node in a :ref:`configuration file <bts-config-ini-eg>`::

    #-- Section to modify in a config.ini file --
	
	# Space-separated list of plugins to activate
	# plugins =


---------------------------


**Categories and plugins' names**

History
=================

`account_history <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/account_history>`_
-----------------------------------
* account_history_plugin.cpp 
* account_history_plugin.hpp


`elasticsearch <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/elasticsearch>`_
-----------------------------------
* elasticsearch_plugin.cpp 
* elasticsearch_plugin.hpp 



`es_objects <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/es_objects>`_
-----------------------------------

* es_objects.cpp
* es_objects.hpp

-------------------------------

Business
========================

`delayed_node <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/delayed_node>`_
------------------------------------

* delayed_node_plugin.hpp
* delayed_node_plugin.hpp

--------------------------

Market Data
=========================


`market_history <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/market_history>`_
----------------------------------------

* market_history_plugin.cpp
* market_history_plugin.hpp




`grouped_objects <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/grouped_orders>`_
-------------------------------------------

* grouped_orders_plugin.cpp
* grouped_orders_plugin.hpp


---------------------------------

Block Producer
========================

`witness <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/witness>`_
----------------------------------------

* witness.cpp
* witness.hpp

-------------------------------

Debug
===========================

`debug_witness <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/debug_witness>`_
-----------------------------------

* debug_api.cpp
* debug_witness.cpp
   

`snapshot <https://github.com/bitshares/bitshares-core/tree/cdc2db30c2f06aaddbfda965ee270b99dc24e0aa/libraries/plugins/snapshot>`_
---------------------------------------

* snapshot.cpp
* snapshot.hpp

------------------------------------

----------------------------------

plugins code flowchart
===========================

The below shares plugins methods and processes.


**witness_plugin methods and flow**

(Created: 2018-11-24) 


.. list-table::
   :widths: 10 20 70
   :header-rows: 1

   * - #
     - File name
     - name
   * - 
     - ..\/libraries\/**plugins\/witness\/witness.cpp**
     - `included several methods below (pdf) <../../../_static/output/witness-plugin-20181124.pdf>`_ 
	   
	   - new_chain_banner()
	   - plugin_set_program_options()
	   - plugin_initialize()
	   - plugin_startup()
	   - shutdown()
	   - stop_block_production()
	   - refresh_witness_key_cache()
	   - schedule_production_loop()
	   - block_production_loop()
	   - maybe_produce)block()
	
	   
	   
	   



|