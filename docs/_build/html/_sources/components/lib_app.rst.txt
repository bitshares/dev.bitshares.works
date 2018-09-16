
.. _lib-app:

********************************
libraries - app 
********************************


.. contents:: Table of Contents
   :local:
   
-------


Directories and files
===========================

For detail information, please check: `graphene::app Namespace Reference <https://bitshares.org/doxygen/namespacegraphene_1_1app.html>`_


For detail information: `graphene::app::plugin Class Reference <https://bitshares.org/doxygen/classgraphene_1_1app_1_1plugin.html>`_


* ../libraries/app/

  - api.cpp 
  - application.cpp 
  - database_api.cpp 
  - plugin.cpp 
  - util/cpp  
  

* ../libraries/app/include/graphene/app/

  - api.hpp
  - api_access.hpp  
  - application.hpp
  - database_api.hpp
  - full_account.hpp
  - plugin.hpp
  - util.hpp
  

--------------------------------------

**xxxxx.hpp**
   
api.hpp
=========================================

struct
-----------------------------

verify_range_result
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
   
   struct verify_range_result
   {
      bool        success;
      uint64_t    min_val;
      uint64_t    max_val;
   };
   
 
verify_range_proof_rewind_result
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
    
   struct verify_range_proof_rewind_result
   {
      bool                          success;
      uint64_t                      min_val;
      uint64_t                      max_val;
      uint64_t                      value_out;
      fc::ecc::blind_factor_type    blind_out;
      string                        message_out;
   };

 
account_asset_balance
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
   struct account_asset_balance
   {
      string          name;
      account_id_type account_id;
      share_type      amount;
   };
 
 
asset_holders  
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
    
   struct asset_holders
   {
      asset_id_type   asset_id;
      int             count;
   };

   
history_operation_detail   
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
    
   struct history_operation_detail {
      uint32_t total_count = 0;
      vector<operation_history_object> operation_history_objs;
   };

   
limit_order_group   
^^^^^^^^^^^^^^^^^^^^^^^^
* summary data of a group of limit orders

.. code-block:: cpp
    
   struct limit_order_group
   {
      limit_order_group( const std::pair<limit_order_group_key,limit_order_group_data>& p )
         :  min_price( p.first.min_price ),
            max_price( p.second.max_price ),
            total_for_sale( p.second.total_for_sale )
            {}
      limit_order_group() {}

      price         min_price; ///< possible lowest price in the group
      price         max_price; ///< possible highest price in the group
      share_type    total_for_sale; ///< total amount of asset for sale, asset id is min_price.base.asset_id
   };
   
class
---------------------------------

For detail information, please check: `graphene::app Namespace Reference <https://bitshares.org/doxygen/namespacegraphene_1_1app.html>`_

* history_api
* block_api
* network_broadcast_api
* network_node_api
* crypto_api
* asset_api
* orders_api
* login_api


 
|

--------------

api_access.hpp
=========================================

struct
------------------

api_access_info   
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
	struct api_access_info
	{
	   std::string password_hash_b64;
	   std::string password_salt_b64;
	   std::vector< std::string > allowed_apis;
	};


api_access   
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
	struct api_access
	{
	   std::map< std::string, api_access_info > permission_map;
	};

|

--------------
	
application.hpp
=========================================

class
--------------------------

For detail information, please check: `graphene::app Namespace Reference <https://bitshares.org/doxygen/namespacegraphene_1_1app.html>`_

For detail information: `graphene::app::plugin Class Reference <https://bitshares.org/doxygen/classgraphene_1_1app_1_1plugin.html>`_



* abstract_plugin
* application_options
* application

|

--------------

database_api.hpp
=========================================

struct
----------------------------

order
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
	struct order
	{
	   string                     price;
	   string                     quote;
	   string                     base;
	};

order_book	
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 	
	struct order_book
	{
	  string                      base;
	  string                      quote;
	  vector< order >             bids;
	  vector< order >             asks;
	};


market_ticker
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
	struct market_ticker
	{
	   time_point_sec             time;
	   string                     base;
	   string                     quote;
	   string                     latest;
	   string                     lowest_ask;
	   string                     highest_bid;
	   string                     percent_change;
	   string                     base_volume;
	   string                     quote_volume;
	};


market_volume
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
	struct market_volume
	{
	   time_point_sec             time;
	   string                     base;
	   string                     quote;
	   string                     base_volume;
	   string                     quote_volume;
	};


market_trade
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
	struct market_trade
	{
	   int64_t                    sequence = 0;
	   fc::time_point_sec         date;
	   string                     price;
	   string                     amount;
	   string                     value;
	   account_id_type            side1_account_id = GRAPHENE_NULL_ACCOUNT;
	   account_id_type            side2_account_id = GRAPHENE_NULL_ACCOUNT;
	};


class
--------------------------------

For detail information, please check: `graphene::app Namespace Reference <https://bitshares.org/doxygen/namespacegraphene_1_1app.html>`_

* database_api


|

--------------

full_account.hpp 
=========================================

struct
-----------------------------------

full_account
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp
 
   struct full_account
   {
      account_object                   account;
      account_statistics_object        statistics;
      string                           registrar_name;
      string                           referrer_name;
      string                           lifetime_referrer_name;
      vector<variant>                  votes;
      optional<vesting_balance_object> cashback_balance;
      vector<account_balance_object>   balances;
      vector<vesting_balance_object>   vesting_balances;
      vector<limit_order_object>       limit_orders;
      vector<call_order_object>        call_orders;
      vector<force_settlement_object>  settle_orders;
      vector<proposal_object>          proposals;
      vector<asset_id_type>            assets;
      vector<withdraw_permission_object> withdraws;
   };


|

--------------

plugin.hpp                 
=========================================

class
-----------------------------------

For detail information: `graphene::app::plugin Class Reference <https://bitshares.org/doxygen/classgraphene_1_1app_1_1plugin.html>`_

* abstract_plugin
* plugin



|

--------------


util.hpp 
=========================================


.. code-block:: cpp

   typedef boost::multiprecision::uint256_t u256;

   u256 to256( const fc::uint128& t );
   fc::uint128 to_capped128( const u256& t );
   string uint128_amount_to_string( const fc::uint128& amount, const uint8_t precision );
   string price_to_string( const price& _price, const uint8_t base_precision, const uint8_t quote_precision);
   string price_diff_percent_string( const price& old_price, const price& new_price );


   
   





|

