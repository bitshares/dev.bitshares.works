
.. _code-flow-chart:

BitShares-Core 
=====================

If someone wants to know how each method's logic has been constructed, it might be interesting to check out. The below files were created based on a specific Release files. Please know that 'Release #'.


Code flows 
---------------

Release 2.0.180612
^^^^^^^^^^^^^^^^^^^^^^

 


.. list-table::
   :widths: 10 20 70
   :header-rows: 1

   * - #
     - File name
     - name
   * - 
     - ..\/libraries\/**app\/application.cpp**
     - `startup() (pdf) <../../_static/output/application-cpp-startup.pdf>`_ 
   * - 
     - ..\/libraries\/**app\/application.cpp**
     - `create_example_genesis() (pdf) <../../_static/output/application-cpp-ns-detail.pdf>`_ 
   * - 
     - ..\/libraries\/**app\/application.cpp**
     - `included several methods below (pdf) <../../_static/output/application-cpp-mix.pdf>`_ 
	   
	   - get_api_access_info() 
	   - has_item()
	   - handle_block()
	   - handle_transaction()
	   - is_included_block()
	   - get_block_ids()
	   - get_blockchain_synopsis()
	   
   * - 
     - ..\/libraries\/**app\/api.cpp**
     - `included several methods below (pdf) <../../_static/output/api-cpp-2.pdf>`_ 
       
	   - login() 
	   - enable_api()
	   - network_broadcast_api()
	   - on_applied_block()
	   - broadcast_transaction()
	   - broadcast_transaction_synchronous()
	   - broadcast_block()
	   - broadcast_transaction_with_callback()
	   - get_info()
	   - add_node()
	   - get_fill_order_history()
	   - get_account_history()
	   - get_account_history_operations( )
	   - get_relative_account_history()
	   - get_account_history_by_operations()
	   - get_market_history()

	 
   * - 
     - ..\/programs\/**cli_wallet\/main.cpp**
     - `main()  (pdf) <../../_static/output/cli_wallet_exe-codeflow.pdf>`_ 
   * - 
     - ..\/programs\/**witness_wallet\/main.cpp**
     - `included several methods below (pdf) <../../_static/output/witness_node_ex-codeflows.pdf>`_ 
	   
	   - structure
	   - load_config_file()
	   - create_new_config_file()
	   - main()

	 
|

|	  	 
	 
