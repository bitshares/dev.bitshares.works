
.. _lib-evaluators:

*******************************************
Evaluators
*******************************************

This section shows what types of evaluators BitShare-Core offers.

File directory: (..\/ibraries/chain/include/graphene/chain/xxx.hpp)


.. contents:: Table of Contents
   :local:
   
-------

account_evaluator 
===============================

account_create_evaluator 
---------------------------------------

.. code-block:: cpp 

	class account_create_evaluator : public evaluator<account_create_evaluator>
	{
	public:
	   typedef account_create_operation operation_type;

	   void_result do_evaluate( const account_create_operation& o );
	   object_id_type do_apply( const account_create_operation& o ) ;
	};


account_update_evaluator
---------------------------------------

.. code-block:: cpp 

	class account_update_evaluator : public evaluator<account_update_evaluator>
	{
	public:
	   typedef account_update_operation operation_type;

	   void_result do_evaluate( const account_update_operation& o );
	   void_result do_apply( const account_update_operation& o );

	   const account_object* acnt;
	};


account_upgrade_evaluator
---------------------------------------

.. code-block:: cpp 

	class account_upgrade_evaluator : public evaluator<account_upgrade_evaluator>
	{
	public:
	   typedef account_upgrade_operation operation_type;

	   void_result do_evaluate(const operation_type& o);
	   void_result do_apply(const operation_type& o);

	   const account_object* account;
	};

account_whitelist_evaluator
---------------------------------------

.. code-block:: cpp 

	class account_whitelist_evaluator : public evaluator<account_whitelist_evaluator>
	{
	public:
	   typedef account_whitelist_operation operation_type;

	   void_result do_evaluate( const account_whitelist_operation& o);
	   void_result do_apply( const account_whitelist_operation& o);

	   const account_object* listed_account;
	};



assert_evaluator 
===============================

assert_evaluator 
---------------------------------------

.. code-block:: cpp 

	class assert_evaluator : public evaluator<assert_evaluator>
	{
	  public:
		 typedef assert_operation operation_type;

		 void_result do_evaluate( const assert_operation& o );
		 void_result do_apply( const assert_operation& o );
	};


asset_evaluator
===============================

asset_create_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_create_evaluator : public evaluator<asset_create_evaluator>
   {
      public:
         typedef asset_create_operation operation_type;

         void_result do_evaluate( const asset_create_operation& o );
         object_id_type do_apply( const asset_create_operation& o );

         /** override the default behavior defined by generic_evalautor which is to
          * post the fee to fee_paying_account_stats.pending_fees
          */
         virtual void pay_fee() override;
      private:
         bool fee_is_odd;
   };

asset_issue_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_issue_evaluator : public evaluator<asset_issue_evaluator>
   {
      public:
         typedef asset_issue_operation operation_type;
         void_result do_evaluate( const asset_issue_operation& o );
         void_result do_apply( const asset_issue_operation& o );

         const asset_dynamic_data_object* asset_dyn_data = nullptr;
         const account_object*            to_account = nullptr;
   };


asset_reserve_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_reserve_evaluator : public evaluator<asset_reserve_evaluator>
   {
      public:
         typedef asset_reserve_operation operation_type;
         void_result do_evaluate( const asset_reserve_operation& o );
         void_result do_apply( const asset_reserve_operation& o );

         const asset_dynamic_data_object* asset_dyn_data = nullptr;
         const account_object*            from_account = nullptr;
   };

   
asset_update_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_update_evaluator : public evaluator<asset_update_evaluator>
   {
      public:
         typedef asset_update_operation operation_type;

         void_result do_evaluate( const asset_update_operation& o );
         void_result do_apply( const asset_update_operation& o );

         const asset_object* asset_to_update = nullptr;
   };

asset_update_issuer_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_update_issuer_evaluator : public evaluator<asset_update_issuer_evaluator>
   {
      public:
         typedef asset_update_issuer_operation operation_type;

         void_result do_evaluate( const asset_update_issuer_operation& o );
         void_result do_apply( const asset_update_issuer_operation& o );

         const asset_object* asset_to_update = nullptr;
   };
   
asset_update_bitasset_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_update_bitasset_evaluator : public evaluator<asset_update_bitasset_evaluator>
   {
      public:
         typedef asset_update_bitasset_operation operation_type;

         void_result do_evaluate( const asset_update_bitasset_operation& o );
         void_result do_apply( const asset_update_bitasset_operation& o );

         const asset_bitasset_data_object* bitasset_to_update = nullptr;
         const asset_object* asset_to_update = nullptr;
   };
   

asset_update_feed_producers_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_update_feed_producers_evaluator : public evaluator<asset_update_feed_producers_evaluator>
   {
      public:
         typedef asset_update_feed_producers_operation operation_type;

         void_result do_evaluate( const operation_type& o );
         void_result do_apply( const operation_type& o );

         const asset_bitasset_data_object* bitasset_to_update = nullptr;
   };

asset_fund_fee_pool_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_fund_fee_pool_evaluator : public evaluator<asset_fund_fee_pool_evaluator>
   {
      public:
         typedef asset_fund_fee_pool_operation operation_type;

         void_result do_evaluate(const asset_fund_fee_pool_operation& op);
         void_result do_apply(const asset_fund_fee_pool_operation& op);

         const asset_dynamic_data_object* asset_dyn_data = nullptr;
   };

asset_global_settle_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_global_settle_evaluator : public evaluator<asset_global_settle_evaluator>
   {
      public:
         typedef asset_global_settle_operation operation_type;

         void_result do_evaluate(const operation_type& op);
         void_result do_apply(const operation_type& op);

         const asset_object* asset_to_settle = nullptr;
   };
   

asset_settle_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_settle_evaluator : public evaluator<asset_settle_evaluator>
   {
      public:
         typedef asset_settle_operation operation_type;

         void_result do_evaluate(const operation_type& op);
         operation_result do_apply(const operation_type& op);

         const asset_object* asset_to_settle = nullptr;
   };

   
asset_publish_feeds_evaluator
----------------------------------------------

.. code-block:: cpp 

   class asset_publish_feeds_evaluator : public evaluator<asset_publish_feeds_evaluator>
   {
      public:
         typedef asset_publish_feed_operation operation_type;

         void_result do_evaluate( const asset_publish_feed_operation& o );
         void_result do_apply( const asset_publish_feed_operation& o );

         const asset_object* asset_ptr = nullptr;
         const asset_bitasset_data_object* bitasset_ptr = nullptr;
   };
  
  
asset_claim_fees_evaluator
----------------------------------------------
.. code-block:: cpp 

   class asset_claim_fees_evaluator : public evaluator<asset_claim_fees_evaluator>
   {
      public:
         typedef asset_claim_fees_operation operation_type;

         void_result do_evaluate( const asset_claim_fees_operation& o );
         void_result do_apply( const asset_claim_fees_operation& o );
   };

asset_claim_pool_evaluator
----------------------------------------------

.. code-block:: cpp 

 class asset_claim_pool_evaluator : public evaluator<asset_claim_pool_evaluator>
   {
      public:
         typedef asset_claim_pool_operation operation_type;

         void_result do_evaluate( const asset_claim_pool_operation& o );
         void_result do_apply( const asset_claim_pool_operation& o );
   };
   
   
   
balance_evaluator
=====================================

balance_claim_evaluator
----------------------------------------------

.. code-block:: cpp 

	class balance_claim_evaluator : public evaluator<balance_claim_evaluator>
	{
	public:
	   typedef balance_claim_operation operation_type;

	   const balance_object* balance = nullptr;

	   void_result do_evaluate(const balance_claim_operation& op);

	   /**
		* @note the fee is always 0 for this particular operation because once the
		* balance is claimed it frees up memory and it cannot be used to spam the network
		*/
	   void_result do_apply(const balance_claim_operation& op);
	};




committee_member_evaluator
=====================================

* committee_member_create_evaluator
* committee_member_update_evaluator
* committee_member_update_global_parameters_evaluator


confidential_evalustor
======================================

* transfer_to_blind_evaluator
* transfer_from_blind_evaluator
* blind_transfer_evaluator

custom_evaluator
=======================================

* custom_evaluator

evaluator
==================================

* generic_evaluator
* op_evaluator

market_evaluator
=============================

.. code-block:: cpp 

   class account_object;
   class asset_object;
   class asset_bitasset_data_object;
   class call_order_object;
   struct bid_collateral_operation;
   struct call_order_update_operation;
   struct limit_order_cancel_operation;
   struct limit_order_create_operation;
   

limit_order_create_evaluator
----------------------------------------------

.. code-block:: cpp 

   class limit_order_create_evaluator : public evaluator<limit_order_create_evaluator>
   {
      public:
         typedef limit_order_create_operation operation_type;

         void_result do_evaluate( const limit_order_create_operation& o );
         object_id_type do_apply( const limit_order_create_operation& o );

         asset calculate_market_fee( const asset_object* aobj, const asset& trade_amount );

         /** override the default behavior defined by generic_evalautor
          */
         virtual void convert_fee() override;

         /** override the default behavior defined by generic_evalautor which is to
          * post the fee to fee_paying_account_stats.pending_fees
          */
         virtual void pay_fee() override;

         share_type                          _deferred_fee  = 0;
         asset                               _deferred_paid_fee;
         const limit_order_create_operation* _op            = nullptr;
         const account_object*               _seller        = nullptr;
         const asset_object*                 _sell_asset    = nullptr;
         const asset_object*                 _receive_asset = nullptr;
   };
   
   
limit_order_cancel_evaluator
----------------------------------------------

.. code-block:: cpp 

   class limit_order_cancel_evaluator : public evaluator<limit_order_cancel_evaluator>
   {
      public:
         typedef limit_order_cancel_operation operation_type;

         void_result do_evaluate( const limit_order_cancel_operation& o );
         asset do_apply( const limit_order_cancel_operation& o );

         const limit_order_object* _order;
   };


call_order_update_evaluator
----------------------------------------------

.. code-block:: cpp 

   class call_order_update_evaluator : public evaluator<call_order_update_evaluator>
   {
      public:
         typedef call_order_update_operation operation_type;

         void_result do_evaluate( const call_order_update_operation& o );
         object_id_type do_apply( const call_order_update_operation& o );

         bool _closing_order = false;
         const asset_object* _debt_asset = nullptr;
         const account_object* _paying_account = nullptr;
         const call_order_object* _order = nullptr;
         const asset_bitasset_data_object* _bitasset_data = nullptr;
   };
   
   
bid_collateral_evaluator
----------------------------------------------

.. code-block:: cpp 

   class bid_collateral_evaluator : public evaluator<bid_collateral_evaluator>
   {
      public:
         typedef bid_collateral_operation operation_type;

         void_result do_evaluate( const bid_collateral_operation& o );
         void_result do_apply( const bid_collateral_operation& o );

         const asset_object* _debt_asset = nullptr;
         const asset_bitasset_data_object* _bitasset_data = nullptr;
         const account_object* _paying_account = nullptr;
         const collateral_bid_object* _bid = nullptr;
   };


proposal_evaluator
=================================

* proposal_create_evaluator
* proposal_update_evaluator
* proposal_delete_evaluator


transfer_evaluator
============================

* transfer_evaluator
* override_transfer_evaluator

vesting_balance_evaluator
==============================

* vesting_balance_create_evaluator
* vesting_balance_withdraw_evaluator

withdraw_permission_evaluator
======================================

* withdraw_permission_create_evaluator
* withdraw_permission_claim_evaluator
* withdraw_permission_update_evaluator
* withdraw_permission_delete_evaluator


witness_evaluator
================================

* witness_create_evaluator
* witness_update_evaluator

worker_evaluator
=================================

* worker_create_evaluator




|