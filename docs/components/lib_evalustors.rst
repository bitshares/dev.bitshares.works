
.. _lib-evaluators:

*******************************************
Evaluators
*******************************************

This section shows what types of evaluators BitShare-Core offers.

File directory: (..\libraries/chain/include/graphene/chain/xxx.hpp)


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

|

---------

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


|

---------

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
   }

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

         const asset_object* asset_to_update = nullptr;
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
   
 
|

---------
  
   
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



|

---------


committee_member_evaluator
=====================================

committee_member_create_evaluator
----------------------------------------------

.. code-block:: cpp 

   class committee_member_create_evaluator : public evaluator<committee_member_create_evaluator>
   {
      public:
         typedef committee_member_create_operation operation_type;

         void_result do_evaluate( const committee_member_create_operation& o );
         object_id_type do_apply( const committee_member_create_operation& o );
   };


committee_member_update_evaluator
----------------------------------------------

.. code-block:: cpp 

   class committee_member_update_evaluator : public evaluator<committee_member_update_evaluator>
   {
      public:
         typedef committee_member_update_operation operation_type;

         void_result do_evaluate( const committee_member_update_operation& o );
         void_result do_apply( const committee_member_update_operation& o );
   };
   


committee_member_update_global_parameters_evaluator
----------------------------------------------

.. code-block:: cpp 

   class committee_member_update_global_parameters_evaluator : public evaluator<committee_member_update_global_parameters_evaluator>
   {
      public:
         typedef committee_member_update_global_parameters_operation operation_type;

         void_result do_evaluate( const committee_member_update_global_parameters_operation& o );
         void_result do_apply( const committee_member_update_global_parameters_operation& o );
   };



|

---------

confidential_evalustor
======================================

.. code-block:: cpp 

	struct transfer_to_blind_operation;
	struct transfer_from_blind_operation;
	struct blind_transfer_operation;


transfer_to_blind_evaluator
----------------------------------------------

.. code-block:: cpp 

	class transfer_to_blind_evaluator : public evaluator<transfer_to_blind_evaluator>
	{
	   public:
		  typedef transfer_to_blind_operation operation_type;

		  void_result do_evaluate( const transfer_to_blind_operation& o );
		  void_result do_apply( const transfer_to_blind_operation& o ) ;

		  virtual void pay_fee() override;
	};

transfer_from_blind_evaluator
----------------------------------------------

.. code-block:: cpp 

	class transfer_from_blind_evaluator : public evaluator<transfer_from_blind_evaluator>
	{
	   public:
		  typedef transfer_from_blind_operation operation_type;

		  void_result do_evaluate( const transfer_from_blind_operation& o );
		  void_result do_apply( const transfer_from_blind_operation& o ) ;

		  virtual void pay_fee() override;
	};


blind_transfer_evaluator
----------------------------------------------

.. code-block:: cpp 

	class blind_transfer_evaluator : public evaluator<blind_transfer_evaluator>
	{
	   public:
		  typedef blind_transfer_operation operation_type;

		  void_result do_evaluate( const blind_transfer_operation& o );
		  void_result do_apply( const blind_transfer_operation& o ) ;

		  virtual void pay_fee() override;
	};


|

---------

custom_evaluator
=======================================

custom_evaluator
----------------------------------------------

.. code-block:: cpp 

   class custom_evaluator : public evaluator<custom_evaluator>
   {
      public:
         typedef custom_operation operation_type;

         void_result do_evaluate( const custom_operation& o ){ return void_result(); }
         void_result do_apply( const custom_operation& o ){ return void_result(); }
   };


|

---------

evaluator
==================================

.. code-block:: cpp 

   class database;
   struct signed_transaction;
   class generic_evaluator;
   class transaction_evaluation_state;
   
generic_evaluator
----------------------------------------------

.. code-block:: cpp 

   class generic_evaluator
   {
   public:
      virtual ~generic_evaluator(){}

      virtual int get_type()const = 0;
      virtual operation_result start_evaluate(transaction_evaluation_state& eval_state, const operation& op, bool apply);

      /**
       * @note derived classes should ASSUME that the default validation that is
       * indepenent of chain state should be performed by op.validate() and should
       * not perform these extra checks.
       */
      virtual operation_result evaluate(const operation& op) = 0;
      virtual operation_result apply(const operation& op) = 0;

      /**
       * Routes the fee to where it needs to go.  The default implementation
       * routes the fee to the account_statistics_object of the fee_paying_account.
       *
       * Before pay_fee() is called, the fee is computed by prepare_fee() and has been
       * moved out of the fee_paying_account and (if paid in a non-CORE asset) converted
       * by the asset's fee pool.
       *
       * Therefore, when pay_fee() is called, the fee only exists in this->core_fee_paid.
       * So pay_fee() need only increment the receiving balance.
       *
       * The default implementation simply calls account_statistics_object->pay_fee() to
       * increment pending_fees or pending_vested_fees.
       */
      virtual void pay_fee();

      database& db()const;

      //void check_required_authorities(const operation& op);
   protected:
      /**
       * @brief Fetch objects relevant to fee payer and set pointer members
       * @param account_id Account which is paying the fee
       * @param fee The fee being paid. May be in assets other than core.
       *
       * This method verifies that the fee is valid and sets the object pointer members and the fee fields. It should
       * be called during do_evaluate.
       *
       * In particular, core_fee_paid field is set by prepare_fee().
       */
      void prepare_fee(account_id_type account_id, asset fee);

      /**
       * Convert the fee into BTS through the exchange pool.
       *
       * Reads core_fee_paid field for how much CORE is deducted from the exchange pool,
       * and fee_from_account for how much USD is added to the pool.
       *
       * Since prepare_fee() does the validation checks ensuring the account and fee pool
       * have sufficient balance and the exchange rate is correct,
       * those validation checks are not replicated here.
       *
       * Rather than returning a value, this method fills in core_fee_paid field.
       */
      virtual void convert_fee();

      object_id_type get_relative_id( object_id_type rel_id )const;

      /**
       * pay_fee() for FBA subclass should simply call this method
       */
      void pay_fba_fee( uint64_t fba_id );

      // the next two functions are helpers that allow template functions declared in this 
      // header to call db() without including database.hpp, which would
      // cause a circular dependency
      share_type calculate_fee_for_operation(const operation& op) const;
      void db_adjust_balance(const account_id_type& fee_payer, asset fee_from_account);

      asset                            fee_from_account;
      share_type                       core_fee_paid;
      const account_object*            fee_paying_account = nullptr;
      const account_statistics_object* fee_paying_account_statistics = nullptr;
      const asset_object*              fee_asset          = nullptr;
      const asset_dynamic_data_object* fee_asset_dyn_data = nullptr;
      transaction_evaluation_state*    trx_state;
   };

   
op_evaluator
----------------------------------------------

.. code-block:: cpp 

   class op_evaluator
   {
   public:
      virtual ~op_evaluator(){}
      virtual operation_result evaluate(transaction_evaluation_state& eval_state, const operation& op, bool apply) = 0;
   };



|

---------

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
   }
   
   
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


|

---------

proposal_evaluator
=================================

proposal_create_evaluator
----------------------------------------------

.. code-block:: cpp 

   class proposal_create_evaluator : public evaluator<proposal_create_evaluator>
   {
      public:
         typedef proposal_create_operation operation_type;

         void_result do_evaluate( const proposal_create_operation& o );
         object_id_type do_apply( const proposal_create_operation& o );

         transaction _proposed_trx;
   };


proposal_update_evaluator
----------------------------------------------

.. code-block:: cpp 

   class proposal_update_evaluator : public evaluator<proposal_update_evaluator>
   {
      public:
         typedef proposal_update_operation operation_type;

         void_result do_evaluate( const proposal_update_operation& o );
         void_result do_apply( const proposal_update_operation& o );

         const proposal_object* _proposal = nullptr;
         processed_transaction _processed_transaction;
         bool _executed_proposal = false;
         bool _proposal_failed = false;
   };


proposal_delete_evaluator
----------------------------------------------

.. code-block:: cpp 

   class proposal_delete_evaluator : public evaluator<proposal_delete_evaluator>
   {
      public:
         typedef proposal_delete_operation operation_type;

         void_result do_evaluate( const proposal_delete_operation& o );
         void_result do_apply(const proposal_delete_operation&);

         const proposal_object* _proposal = nullptr;
   };


|

---------

transaction_evaluation_state
============================


transaction_evaluation_state
----------------------------------------------

- Place holder for state tracked while processing a transaction. This class provides helper methods that are common to many different operations and also tracks which keys have signed the transaction.
	
.. code-block:: cpp 

   class database;
   struct signed_transaction;

   class transaction_evaluation_state
   {
      public:
         transaction_evaluation_state( database* db = nullptr )
         :_db(db){}


         database& db()const { assert( _db ); return *_db; }
         vector<operation_result> operation_results;

         const signed_transaction*        _trx = nullptr;
         database*                        _db = nullptr;
         bool                             _is_proposed_trx = false;
         bool                             skip_fee = false;
         bool                             skip_fee_schedule_check = false;
   };

|

---------

transfer_evaluator
============================

transfer_evaluator
----------------------------------------------

.. code-block:: cpp 

   class transfer_evaluator : public evaluator<transfer_evaluator>
   {
      public:
         typedef transfer_operation operation_type;

         void_result do_evaluate( const transfer_operation& o );
         void_result do_apply( const transfer_operation& o );
   };


override_transfer_evaluator
----------------------------------------------

.. code-block:: cpp 

   class override_transfer_evaluator : public evaluator<override_transfer_evaluator>
   {
      public:
         typedef override_transfer_operation operation_type;

         void_result do_evaluate( const override_transfer_operation& o );
         void_result do_apply( const override_transfer_operation& o );
   };


|

---------

vesting_balance_evaluator
==============================

.. code-block:: cpp 

	class vesting_balance_create_evaluator;
	class vesting_balance_withdraw_evaluator;

vesting_balance_create_evaluator
----------------------------------------------

.. code-block:: cpp 

	class vesting_balance_create_evaluator : public evaluator<vesting_balance_create_evaluator>
	{
		public:
			typedef vesting_balance_create_operation operation_type;

			void_result do_evaluate( const vesting_balance_create_operation& op );
			object_id_type do_apply( const vesting_balance_create_operation& op );
	};

vesting_balance_withdraw_evaluator
----------------------------------------------

.. code-block:: cpp 

	class vesting_balance_withdraw_evaluator : public evaluator<vesting_balance_withdraw_evaluator>
	{
		public:
			typedef vesting_balance_withdraw_operation operation_type;

			void_result do_evaluate( const vesting_balance_withdraw_operation& op );
			void_result do_apply( const vesting_balance_withdraw_operation& op );
	};



|

---------

withdraw_permission_evaluator
======================================

withdraw_permission_create_evaluator
----------------------------------------------

.. code-block:: cpp 

	class withdraw_permission_create_evaluator : public evaluator<withdraw_permission_create_evaluator>
	{
	public:
	   typedef withdraw_permission_create_operation operation_type;

	   void_result do_evaluate( const operation_type& op );
	   object_id_type do_apply( const operation_type& op );
	};


withdraw_permission_claim_evaluator
----------------------------------------------

.. code-block:: cpp 

	class withdraw_permission_claim_evaluator : public evaluator<withdraw_permission_claim_evaluator>
	{
	public:
	   typedef withdraw_permission_claim_operation operation_type;

	   void_result do_evaluate( const operation_type& op );
	   void_result do_apply( const operation_type& op );
	};



withdraw_permission_update_evaluator
----------------------------------------------

.. code-block:: cpp 

	class withdraw_permission_update_evaluator : public evaluator<withdraw_permission_update_evaluator>
	{
	public:
	   typedef withdraw_permission_update_operation operation_type;

	   void_result do_evaluate( const operation_type& op );
	   void_result do_apply( const operation_type& op );
	};


withdraw_permission_delete_evaluator
----------------------------------------------

.. code-block:: cpp 

	class withdraw_permission_delete_evaluator : public evaluator<withdraw_permission_delete_evaluator>
	{
	public:
	   typedef withdraw_permission_delete_operation operation_type;

	   void_result do_evaluate( const operation_type& op );
	   void_result do_apply( const operation_type& op );
	};



|

---------

witness_evaluator
================================

witness_create_evaluator
----------------------------------------------

.. code-block:: cpp 

   class witness_create_evaluator : public evaluator<witness_create_evaluator>
   {
      public:
         typedef witness_create_operation operation_type;

         void_result do_evaluate( const witness_create_operation& o );
         object_id_type do_apply( const witness_create_operation& o );
   };


witness_update_evaluator
----------------------------------------------

.. code-block:: cpp 

   class witness_update_evaluator : public evaluator<witness_update_evaluator>
   {
      public:
         typedef witness_update_operation operation_type;

         void_result do_evaluate( const witness_update_operation& o );
         void_result do_apply( const witness_update_operation& o );
   };



|

---------

worker_evaluator
=================================

worker_create_evaluator
----------------------------------------------

.. code-block:: cpp 

   class worker_create_evaluator : public evaluator<worker_create_evaluator>
   {
      public:
         typedef worker_create_operation operation_type;

         void_result do_evaluate( const operation_type& o );
         object_id_type do_apply( const operation_type& o );
   };





|