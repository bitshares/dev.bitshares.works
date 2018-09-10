
.. _lib-objects:

*********************
Object Elements
*********************

*BitShares Core - graphene::chain*


This document purpose: to make a list of BitShares objects and provide each object structure and the purpose. 


.. contents:: Table of Contents
   :local:
   
------

Object Types
=================

*enum graphene::chain::object_type*

(../libraries/chain/protocol/types.hpp)

List all object types from all namespaces here so they can be easily reflected and displayed in debug output. If a 3rd party wants to extend the core code then they will have to change the packed_object::type field from enum_type to uint16 to avoid warnings when converting packed_objects to/from json. 

Enumerator 
--------------

- null_object_type  
- base_object_type 	 
- account_object_type  
- asset_object_type  	
- force_settlement_object_type 	 
- committee_member_object_type  	
- witness_object_type   	
- limit_order_object_type   
- call_order_object_type   	
- custom_object_type   	
- proposal_object_type   
- operation_history_object_type  	
- withdraw_permission_object_type  
- vesting_balance_object_typev  
- worker_object_type 	  
- balance_object_type  	
- OBJECT_TYPE_COUNT: Sentry value which contains the number of different object types. 


------------

Object Names and Detailed Descriptions
============================================

*graphene::chain Namespace: Class - Objects*

(../libraries/chain/include/graphene/chain/) 

**Detailed Descriptions**


Account 
================



`account_balance_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1account__balance__object.html>`_
-----------------------------
- Tracks the balance of a single account/asset pair
- This object is indexed on owner and asset_type so that black swan events in asset_type can be processed quickly

.. code-block:: cpp 

   class account_balance_object : public abstract_object<account_balance_object>
   {
      public:
         static const uint8_t space_id = implementation_ids;
         static const uint8_t type_id  = impl_account_balance_object_type;

         account_id_type   owner;
         asset_id_type     asset_type;
         share_type        balance;
         bool              maintenance_flag = false; ///< Whether need to process this balance object in maintenance interval

         asset get_balance()const { return asset(balance, asset_type); }
         void  adjust_balance(const asset& delta);
   };  





`account_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1account__object.html>`_ 
-----------------------------------------------------
- This class represents an account on the object graphAccounts are the primary unit of authority on the graphene system. Users must have an account in order to use assets, trade in the markets, vote for committee_members, etc 

.. code-block:: cpp 


   class account_object : public graphene::db::abstract_object<account_object>
   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id  = account_object_type;

         /**
          * The time at which this account's membership expires.
          * If set to any time in the past, the account is a basic account.
          * If set to time_point_sec::maximum(), the account is a lifetime member.
          * If set to any time not in the past less than time_point_sec::maximum(), the account is an annual member.
          *
          * See @ref is_lifetime_member, @ref is_basic_account, @ref is_annual_member, and @ref is_member
          */
         time_point_sec membership_expiration_date;

         ///The account that paid the fee to register this account. Receives a percentage of referral rewards.
         account_id_type registrar;
         /// The account credited as referring this account. Receives a percentage of referral rewards.
         account_id_type referrer;
         /// The lifetime member at the top of the referral tree. Receives a percentage of referral rewards.
         account_id_type lifetime_referrer;

         /// Percentage of fee which should go to network.
         uint16_t network_fee_percentage = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE;
         /// Percentage of fee which should go to lifetime referrer.
         uint16_t lifetime_referrer_fee_percentage = 0;
         /// Percentage of referral rewards (leftover fee after paying network and lifetime referrer) which should go
         /// to referrer. The remainder of referral rewards goes to the registrar.
         uint16_t referrer_rewards_percentage = 0;

         /// The account's name. This name must be unique among all account names on the graph. May not be empty.
         string name;

         /**
          * The owner authority represents absolute control over the account. Usually the keys in this authority will
          * be kept in cold storage, as they should not be needed very often and compromise of these keys constitutes
          * complete and irrevocable loss of the account. Generally the only time the owner authority is required is to
          * update the active authority.
          */
         authority owner;
         /// The owner authority contains the hot keys of the account. This authority has control over nearly all
         /// operations the account may perform.
         authority active;

         typedef account_options  options_type;
         account_options options;

         /// The reference implementation records the account's statistics in a separate object. This field contains the
         /// ID of that object.
         account_statistics_id_type statistics;

         /**
          * This is a set of all accounts which have 'whitelisted' this account. Whitelisting is only used in core
          * validation for the purpose of authorizing accounts to hold and transact in whitelisted assets. This
          * account cannot update this set, except by transferring ownership of the account, which will clear it. Other
          * accounts may add or remove their IDs from this set.
          */
         flat_set<account_id_type> whitelisting_accounts;

         /**
          * Optionally track all of the accounts this account has whitelisted or blacklisted, these should
          * be made Immutable so that when the account object is cloned no deep copy is required.  This state is
          * tracked for GUI display purposes.
          *
          * TODO: move white list tracking to its own multi-index container rather than having 4 fields on an
          * account.   This will scale better because under the current design if you whitelist 2000 accounts,
          * then every time someone fetches this account object they will get the full list of 2000 accounts.
          */
         ///@{
         set<account_id_type> whitelisted_accounts;
         set<account_id_type> blacklisted_accounts;
         ///@}


         /**
          * This is a set of all accounts which have 'blacklisted' this account. Blacklisting is only used in core
          * validation for the purpose of forbidding accounts from holding and transacting in whitelisted assets. This
          * account cannot update this set, and it will be preserved even if the account is transferred. Other accounts
          * may add or remove their IDs from this set.
          */
         flat_set<account_id_type> blacklisting_accounts;

         /**
          * Vesting balance which receives cashback_reward deposits.
          */
         optional<vesting_balance_id_type> cashback_vb;

         special_authority owner_special_authority = no_special_authority();
         special_authority active_special_authority = no_special_authority();

         /**
          * This flag is set when the top_n logic sets both authorities,
          * and gets reset when authority or special_authority is set.
          */
         uint8_t top_n_control_flags = 0;
         static const uint8_t top_n_control_owner  = 1;
         static const uint8_t top_n_control_active = 2;

         /**
          * This is a set of assets which the account is allowed to have.
          * This is utilized to restrict buyback accounts to the assets that trade in their markets.
          * In the future we may expand this to allow accounts to e.g. voluntarily restrict incoming transfers.
          */
         optional< flat_set<asset_id_type> > allowed_assets;

         bool has_special_authority()const
         {
            return (owner_special_authority.which() != special_authority::tag< no_special_authority >::value)
                || (active_special_authority.which() != special_authority::tag< no_special_authority >::value);
         }

         template<typename DB>
         const vesting_balance_object& cashback_balance(const DB& db)const
         {
            FC_ASSERT(cashback_vb);
            return db.get(*cashback_vb);
         }

         /// @return true if this is a lifetime member account; false otherwise.
         bool is_lifetime_member()const
         {
            return membership_expiration_date == time_point_sec::maximum();
         }
         /// @return true if this is a basic account; false otherwise.
         bool is_basic_account(time_point_sec now)const
         {
            return now > membership_expiration_date;
         }
         /// @return true if the account is an unexpired annual member; false otherwise.
         /// @note This method will return false for lifetime members.
         bool is_annual_member(time_point_sec now)const
         {
            return !is_lifetime_member() && !is_basic_account(now);
         }
         /// @return true if the account is an annual or lifetime member; false otherwise.
         bool is_member(time_point_sec now)const
         {
            return !is_basic_account(now);
         }

         account_id_type get_id()const { return id; }
   };
   
  
`account_statistics_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1account__statistics__object.html>`_ 
-----------------------------------------------------
- This object contains regularly updated statistical data about an account. It is provided for the purpose of separating the account data that changes frequently from the account data that is mostly static, which will minimize the amount of data that must be backed up as part of the undo history everytime a transfer is made. 

.. code-block:: cpp 

   class account_statistics_object : public graphene::db::abstract_object<account_statistics_object>
   {
      public:
         static const uint8_t space_id = implementation_ids;
         static const uint8_t type_id  = impl_account_statistics_object_type;

         account_id_type  owner;

         string           name; ///< redundantly store account name here for better maintenance performance

         /**
          * Keep the most recent operation as a root pointer to a linked list of the transaction history.
          */
         account_transaction_history_id_type most_recent_op;
         /** Total operations related to this account. */
         uint32_t                            total_ops = 0;
         /** Total operations related to this account that has been removed from the database. */
         uint32_t                            removed_ops = 0;

         /**
          * When calculating votes it is necessary to know how much is stored in orders (and thus unavailable for
          * transfers). Rather than maintaining an index of [asset,owner,order_id] we will simply maintain the running
          * total here and update it every time an order is created or modified.
          */
         share_type total_core_in_orders;

         share_type core_in_balance = 0; ///< redundantly store core balance here for better maintenance performance

         bool has_cashback_vb = false; ///< redundantly store this for better maintenance performance

         bool is_voting = false; ///< redundately store whether this account is voting for better maintenance performance

         /// Whether this account owns some CORE asset and is voting
         inline bool has_some_core_voting() const
         {
            return is_voting && ( total_core_in_orders > 0 || core_in_balance > 0 || has_cashback_vb );
         }

         /**
          * Tracks the total fees paid by this account for the purpose of calculating bulk discounts.
          */
         share_type lifetime_fees_paid;

         /**
          * Tracks the fees paid by this account which have not been disseminated to the various parties that receive
          * them yet (registrar, referrer, lifetime referrer, network, etc). This is used as an optimization to avoid
          * doing massive amounts of uint128 arithmetic on each and every operation.
          *
          * These fees will be paid out as vesting cash-back, and this counter will reset during the maintenance
          * interval.
          */
         share_type pending_fees;
         /**
          * Same as @ref pending_fees, except these fees will be paid out as pre-vested cash-back (immediately
          * available for withdrawal) rather than requiring the normal vesting period.
          */
         share_type pending_vested_fees;

         /// Whether this account has pending fees, no matter vested or not
         inline bool has_pending_fees() const { return pending_fees > 0 || pending_vested_fees > 0; }

         /// Whether need to process this account during the maintenance interval
         inline bool need_maintenance() const { return has_some_core_voting() || has_pending_fees(); }

         /// @brief Split up and pay out @ref pending_fees and @ref pending_vested_fees
         void process_fees(const account_object& a, database& d) const;

         /**
          * Core fees are paid into the account_statistics_object by this method
          */
         void pay_fee( share_type core_fee, share_type cashback_vesting_threshold );
   };
   

 

`withdraw_permission_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1withdraw__permission__object.html>`_ 
-----------------------------------------------------
- Grants another account authority to withdraw a limited amount of funds per interval	  
- The primary purpose of this object is to enable recurring payments on the blockchain. An account which wishes to process a recurring payment may use a ``withdraw_permission_claim_operation`` to reference an object of this type and withdraw up to ``withdrawal_limit`` from ``withdraw_from_account``. Only ``authorized_account`` may do this. Any number of withdrawals may be made so long as the total amount withdrawn per period does not exceed the limit for any given period. 

.. code-block:: cpp 

  class withdraw_permission_object : public graphene::db::abstract_object<withdraw_permission_object>
  {
     public:
        static const uint8_t space_id = protocol_ids;
        static const uint8_t type_id  = withdraw_permission_object_type;

        /// The account authorizing @ref authorized_account to withdraw from it
        account_id_type    withdraw_from_account;
        /// The account authorized to make withdrawals from @ref withdraw_from_account
        account_id_type    authorized_account;
        /// The maximum amount which may be withdrawn per period. All withdrawals must be of this asset type
        asset              withdrawal_limit;
        /// The duration of a withdrawal period in seconds
        uint32_t           withdrawal_period_sec = 0;
       /***
        * The beginning of the next withdrawal period
        * WARNING: Due to caching, this value does not always represent the start of the next or current period (because it is only updated after a withdrawal operation such as claim).  For the latest current period, use current_period().
        */
        time_point_sec     period_start_time;
        /// The time at which this withdraw permission expires
        time_point_sec     expiration;

       /***
        * Tracks the total amount
        * WARNING: Due to caching, this value does not always represent the total amount claimed during the current period; it may represent what was claimed during the last claimed period (because it is only updated after a withdrawal operation such as claim).  For the latest current period, use current_period().
        */
        share_type         claimed_this_period;

       /***
        * Determine how much is still available to be claimed during the period that contains a time of interest.  This object and function is mainly intended to be used with the "current" time as a parameter.  The current time can be obtained from the time of the current head of the blockchain.
        */
        asset              available_this_period( fc::time_point_sec current_time )const
        {
           if( current_time >= period_start_time + withdrawal_period_sec )
              return withdrawal_limit;
           return asset(
              ( withdrawal_limit.amount > claimed_this_period )
              ? withdrawal_limit.amount - claimed_this_period
              : 0, withdrawal_limit.asset_id );
        }
   };
   

Authority 
================================

`special_authority_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1special__authority__object.html>`_ 
-----------------------------------------------------
- ``special_authority_object`` only exists to help with a specific indexing problem. We want to be able to iterate over all accounts that contain a special authority. However, accounts which have a special_authority are very rare. So rather than indexing ``ccount_object`` by the special_authority fields (requiring additional bookkeeping for every account), we instead maintain a ``special_authority_object`` pointing to each account which has ``special_authority`` (requiring additional bookkeeping only for every account which has special_authority).
- This class is an implementation detail.    	

.. code-block:: cpp 

	class special_authority_object : public graphene::db::abstract_object<special_authority_object>
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id = impl_special_authority_object_type;

		  account_id_type account;
	};



Asset 
================

 
`asset_dynamic_data_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1asset__dynamic__data__object.html>`_ 
-----------------------------------------------------
- tracks the asset information that changes frequently
- Because the ``asset_object`` is very large it doesn't make sense to save an undo state for all of the parameters that never change. This object factors out the parameters of an asset that change in almost every transaction that involves the asset.
- This object exists as an implementation detail and its ID should never be referenced by a blockchain operation

.. code-block:: cpp 

   class asset_dynamic_data_object : public abstract_object<asset_dynamic_data_object>
   {
      public:
         static const uint8_t space_id = implementation_ids;
         static const uint8_t type_id  = impl_asset_dynamic_data_type;

         /// The number of shares currently in existence
         share_type current_supply;
         share_type confidential_supply; ///< total asset held in confidential balances
         share_type accumulated_fees; ///< fees accumulate to be paid out over time
         share_type fee_pool;         ///< in core asset
   };
   
   
`asset_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1asset__object.html>`_ 
-----------------------------------------------------
- tracks the parameters of an asset
- All assets have a globally unique symbol name that controls how they are traded and an issuer who has authority over the parameters of the asset. 	 

.. code-block:: cpp 

   class asset_object : public graphene::db::abstract_object<asset_object>
   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id  = asset_object_type;

         /// This function does not check if any registered asset has this symbol or not; it simply checks whether the
         /// symbol would be valid.
         /// @return true if symbol is a valid ticker symbol; false otherwise.
         static bool is_valid_symbol( const string& symbol );

         /// @return true if this is a market-issued asset; false otherwise.
         bool is_market_issued()const { return bitasset_data_id.valid(); }
         /// @return true if users may request force-settlement of this market-issued asset; false otherwise
         bool can_force_settle()const { return !(options.flags & disable_force_settle); }
         /// @return true if the issuer of this market-issued asset may globally settle the asset; false otherwise
         bool can_global_settle()const { return options.issuer_permissions & global_settle; }
         /// @return true if this asset charges a fee for the issuer on market operations; false otherwise
         bool charges_market_fees()const { return options.flags & charge_market_fee; }
         /// @return true if this asset may only be transferred to/from the issuer or market orders
         bool is_transfer_restricted()const { return options.flags & transfer_restricted; }
         bool can_override()const { return options.flags & override_authority; }
         bool allow_confidential()const { return !(options.flags & asset_issuer_permission_flags::disable_confidential); }

         /// Helper function to get an asset object with the given amount in this asset's type
         asset amount(share_type a)const { return asset(a, id); }
         /// Convert a string amount (i.e. "123.45") to an asset object with this asset's type
         /// The string may have a decimal and/or a negative sign.
         asset amount_from_string(string amount_string)const;
         /// Convert an asset to a textual representation, i.e. "123.45"
         string amount_to_string(share_type amount)const;
         /// Convert an asset to a textual representation, i.e. "123.45"
         string amount_to_string(const asset& amount)const
         { FC_ASSERT(amount.asset_id == id); return amount_to_string(amount.amount); }
         /// Convert an asset to a textual representation with symbol, i.e. "123.45 USD"
         string amount_to_pretty_string(share_type amount)const
         { return amount_to_string(amount) + " " + symbol; }
         /// Convert an asset to a textual representation with symbol, i.e. "123.45 USD"
         string amount_to_pretty_string(const asset &amount)const
         { FC_ASSERT(amount.asset_id == id); return amount_to_pretty_string(amount.amount); }

         /// Ticker symbol for this asset, i.e. "USD"
         string symbol;
         /// Maximum number of digits after the decimal point (must be <= 12)
         uint8_t precision = 0;
         /// ID of the account which issued this asset.
         account_id_type issuer;

         asset_options options;


         /// Current supply, fee pool, and collected fees are stored in a separate object as they change frequently.
         asset_dynamic_data_id_type  dynamic_asset_data_id;
         /// Extra data associated with BitAssets. This field is non-null if and only if is_market_issued() returns true
         optional<asset_bitasset_data_id_type> bitasset_data_id;

         optional<account_id_type> buyback_account;

         asset_id_type get_id()const { return id; }

         void validate()const
         {
            // UIAs may not be prediction markets, have force settlement, or global settlements
            if( !is_market_issued() )
            {
               FC_ASSERT(!(options.flags & disable_force_settle || options.flags & global_settle));
               FC_ASSERT(!(options.issuer_permissions & disable_force_settle || options.issuer_permissions & global_settle));
            }
         }

         template<class DB>
         const asset_bitasset_data_object& bitasset_data(const DB& db)const
         {
            FC_ASSERT( bitasset_data_id.valid(),
                       "Asset ${a} (${id}) is not a market issued asset.",
                       ("a",this->symbol)("id",this->id) );
            return db.get( *bitasset_data_id );
         }

         template<class DB>
         const asset_dynamic_data_object& dynamic_data(const DB& db)const
         { return db.get(dynamic_asset_data_id); }

         /**
          *  The total amount of an asset that is reserved for future issuance. 
          */
         template<class DB>
         share_type reserved( const DB& db )const
         { return options.max_supply - dynamic_data(db).current_supply; }
   };


   
`budget_record_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1budget__record__object.html>`_ 
-----------------------------------------------------
-   

.. code-block:: cpp 

	struct budget_record
	{
	   uint64_t time_since_last_budget = 0;

	   // sources of budget
	   share_type from_initial_reserve = 0;
	   share_type from_accumulated_fees = 0;
	   share_type from_unused_witness_budget = 0;

	   // witness budget requested by the committee
	   share_type requested_witness_budget = 0;

	   // funds that can be released from reserve at maximum rate
	   share_type total_budget = 0;

	   // sinks of budget, should sum up to total_budget
	   share_type witness_budget = 0;
	   share_type worker_budget = 0;

	   // unused budget
	   share_type leftover_worker_funds = 0;

	   // change in supply due to budget operations
	   share_type supply_delta = 0;
	};

	class budget_record_object;

	class budget_record_object : public graphene::db::abstract_object<budget_record_object>
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id = impl_budget_record_object_type;

		  fc::time_point_sec time;
		  budget_record record;
	};


`buyback_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1buyback__object.html>`_ 
-----------------------------------------------------
- ``buyback_authority_object`` only exists to help with a specific indexing problem. We want to be able to iterate over all assets that have a buyback program. However, assets which have a buyback program are very rare. So rather than indexing ``asset_object`` by the buyback field (requiring additional bookkeeping for every asset), we instead maintain a ``buyback_object`` pointing to each asset which has buyback (requiring additional bookkeeping only for every asset which has buyback).
- This class is an implementation detail.  

.. code-block:: cpp 

	class buyback_object : public graphene::db::abstract_object< buyback_object >
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id = impl_buyback_object_type;

		  asset_id_type asset_to_buy;
	};


BitAsset 
========================

`asset_bitasset_data_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1asset__bitasset__data__object.html>`_ 
-----------------------------------------------------
- contains properties that only apply to bitassets (market issued assets) 	  
 
.. code-block:: cpp 

   class asset_bitasset_data_object : public abstract_object<asset_bitasset_data_object>
   {
      public:
         static const uint8_t space_id = implementation_ids;
         static const uint8_t type_id  = impl_asset_bitasset_data_type;

         /// The asset this object belong to
         asset_id_type asset_id;

         /// The tunable options for BitAssets are stored in this field.
         bitasset_options options;

         /// Feeds published for this asset. If issuer is not committee, the keys in this map are the feed publishing
         /// accounts; otherwise, the feed publishers are the currently active committee_members and witnesses and this map
         /// should be treated as an implementation detail. The timestamp on each feed is the time it was published.
         flat_map<account_id_type, pair<time_point_sec,price_feed>> feeds;
         /// This is the currently active price feed, calculated as the median of values from the currently active
         /// feeds.
         price_feed current_feed;
         /// This is the publication time of the oldest feed which was factored into current_feed.
         time_point_sec current_feed_publication_time;

         /// True if this asset implements a @ref prediction_market
         bool is_prediction_market = false;

         /// This is the volume of this asset which has been force-settled this maintanence interval
         share_type force_settled_volume;
         /// Calculate the maximum force settlement volume per maintenance interval, given the current share supply
         share_type max_force_settlement_volume(share_type current_supply)const;

         /** return true if there has been a black swan, false otherwise */
         bool has_settlement()const { return !settlement_price.is_null(); }

         /**
          *  In the event of a black swan, the swan price is saved in the settlement price, and all margin positions
          *  are settled at the same price with the siezed collateral being moved into the settlement fund. From this
          *  point on no further updates to the asset are permitted (no feeds, etc) and forced settlement occurs
          *  immediately when requested, using the settlement price and fund.
          */
         ///@{
         /// Price at which force settlements of a black swanned asset will occur
         price settlement_price;
         /// Amount of collateral which is available for force settlement
         share_type settlement_fund;
         ///@}

         /// Track whether core_exchange_rate in corresponding asset_object has updated
         bool asset_cer_updated = false;

         /// Track whether core exchange rate in current feed has updated
         bool feed_cer_updated = false;

         /// Whether need to update core_exchange_rate in asset_object
         bool need_to_update_cer() const
         {
            return ( ( feed_cer_updated || asset_cer_updated ) && !current_feed.core_exchange_rate.is_null() );
         }

         /// The time when @ref current_feed would expire
         time_point_sec feed_expiration_time()const
         {
            uint32_t current_feed_seconds = current_feed_publication_time.sec_since_epoch();
            if( std::numeric_limits<uint32_t>::max() - current_feed_seconds <= options.feed_lifetime_sec )
               return time_point_sec::maximum();
            else
               return current_feed_publication_time + options.feed_lifetime_sec;
         }
         bool feed_is_expired_before_hardfork_615(time_point_sec current_time)const
         { return feed_expiration_time() >= current_time; }
         bool feed_is_expired(time_point_sec current_time)const
         { return feed_expiration_time() <= current_time; }
         void update_median_feeds(time_point_sec current_time);
   };
   
 

Balance
==============

`blinded_balance_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1blinded__balance__object.html>`_ 
-----------------------------------------------------
- tracks a blinded balance commitment	  

.. code-block:: cpp 

	class blinded_balance_object : public graphene::db::abstract_object<blinded_balance_object>
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id  = impl_blinded_balance_object_type;

		  fc::ecc::commitment_type                commitment;
		  asset_id_type                           asset_id;
		  authority                               owner;
	};
   

`balance_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1balance__object.html>`_ 
-----------------------------------------------------
-   

.. code-block:: cpp 

   class balance_object : public abstract_object<balance_object>
   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id  = balance_object_type;

         bool is_vesting_balance()const
         { return vesting_policy.valid(); }
         asset available(fc::time_point_sec now)const
         {
            return is_vesting_balance()? vesting_policy->get_allowed_withdraw({balance, now, {}})
                                       : balance;
         }

         address owner;
         asset   balance;
         optional<linear_vesting_policy> vesting_policy;
         time_point_sec last_claim_date;
         asset_id_type asset_type()const { return balance.asset_id; }
   };


Block
=================

`block_summary_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1block__summary__object.html>`_ 
-----------------------------------------------------
- tracks minimal information about past blocks to implement TaPOS
- When attempting to calculate the validity of a transaction we need to lookup a past block and check its block hash and the time it occurred so we can calculate whether the current transaction is valid and at what time it should expire. 

.. code-block:: cpp 

   class block_summary_object : public abstract_object<block_summary_object>
   {
      public:
         static const uint8_t space_id = implementation_ids;
         static const uint8_t type_id  = impl_block_summary_object_type;

         block_id_type      block_id;
   };


Chain
========================

`chain_property_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1chain__property__object.html>`_ 
-----------------------------------------------------
- Contains invariants which are set at genesis and never changed. 	  

.. code-block:: cpp 

	class chain_property_object : public abstract_object<chain_property_object>
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id  = impl_chain_property_object_type;

		  chain_id_type chain_id;
		  immutable_chain_parameters immutable_parameters;
	};



`dynamic_global_property_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1dynamic__global__property__object.html>`_ 
-----------------------------------------------------
- Maintains global state information (committee_member list, current fees)
- This is an implementation detail. The values here are calculated during normal chain operations and reflect the current values of global blockchain properties. 

.. code-block:: cpp 

   class dynamic_global_property_object : public abstract_object<dynamic_global_property_object>
   {
      public:
         static const uint8_t space_id = implementation_ids;
         static const uint8_t type_id  = impl_dynamic_global_property_object_type;

         uint32_t          head_block_number = 0;
         block_id_type     head_block_id;
         time_point_sec    time;
         witness_id_type   current_witness;
         time_point_sec    next_maintenance_time;
         time_point_sec    last_budget_time;
         share_type        witness_budget;
         uint32_t          accounts_registered_this_interval = 0;
         /**
          *  Every time a block is missed this increases by
          *  RECENTLY_MISSED_COUNT_INCREMENT,
          *  every time a block is found it decreases by
          *  RECENTLY_MISSED_COUNT_DECREMENT.  It is
          *  never less than 0.
          *
          *  If the recently_missed_count hits 2*UNDO_HISTORY then no new blocks may be pushed.
          */
         uint32_t          recently_missed_count = 0;

         /**
          * The current absolute slot number.  Equal to the total
          * number of slots since genesis.  Also equal to the total
          * number of missed slots plus head_block_number.
          */
         uint64_t                current_aslot = 0;

         /**
          * used to compute witness participation.
          */
         fc::uint128_t recent_slots_filled;

         /**
          * dynamic_flags specifies chain state properties that can be
          * expressed in one bit.
          */
         uint32_t dynamic_flags = 0;

         uint32_t last_irreversible_block_num = 0;

         enum dynamic_flag_bits
         {
            /**
             * If maintenance_flag is set, then the head block is a
             * maintenance block.  This means
             * get_time_slot(1) - head_block_time() will have a gap
             * due to maintenance duration.
             *
             * This flag answers the question, "Was maintenance
             * performed in the last call to apply_block()?"
             */
            maintenance_flag = 0x01
         };
   };
   

`global_property_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1global__property__object.html>`_ 
-----------------------------------------------------
- Maintains global state information (committee_member list, current fees)
- This is an implementation detail. The values here are set by committee_members to tune the blockchain parameters. 

.. code-block:: cpp 

   class global_property_object : public graphene::db::abstract_object<global_property_object>
   {
      public:
         static const uint8_t space_id = implementation_ids;
         static const uint8_t type_id  = impl_global_property_object_type;

         chain_parameters           parameters;
         optional<chain_parameters> pending_parameters;

         uint32_t                           next_available_vote_id = 0;
         vector<committee_member_id_type>   active_committee_members; // updated once per maintenance interval
         flat_set<witness_id_type>          active_witnesses; // updated once per maintenance interval
         // n.b. witness scheduling is done by witness_schedule object
   };


Committee 
========================

`committee_member_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1committee__member__object.html>`_ 
-----------------------------------------------------
- tracks information about a committee_member account.
- A committee_member is responsible for setting blockchain parameters and has dynamic multi-sig control over the committee account. The current set of active committee_members has control.
- committee_members were separated into a separate object to make iterating over the set of committee_member easy. 

.. code-block:: cpp 

   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id  = committee_member_object_type;

         account_id_type  committee_member_account;
         vote_id_type     vote_id;
         uint64_t         total_votes = 0;
         string           url;
   };

   
FBA
=============	 

`fba_accumulator_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1fba__accumulator__object.html>`_ 
-----------------------------------------------------
- fba_accumulator_object accumulates fees to be paid out via buyback or other FBA mechanism.   

.. code-block:: cpp 

	class fba_accumulator_object : public graphene::db::abstract_object< fba_accumulator_object >
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id = impl_fba_accumulator_object_type;

		  share_type accumulated_fba_fees;
		  optional< asset_id_type > designated_asset;

		  bool is_configured( const database& db )const;
	};

- An object will be created at genesis for each of these FBA accumulators.
 
::

	enum graphene_fba_accumulator_id_enum
	{
	   fba_accumulator_id_transfer_to_blind = 0,
	   fba_accumulator_id_blind_transfer,
	   fba_accumulator_id_transfer_from_blind,
	   fba_accumulator_id_count
	};

History 
=======================

`operation_history_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1operation__history__object.html>`_ 
-----------------------------------------------------
- tracks the history of all logical operations on blockchain state
- All operations and virtual operations result in the creation of an operation_history_object that is maintained on disk as a stack. Each real or virtual operation is assigned a unique ID / sequence number that it can be referenced by.

.. Note:: by default these objects are not tracked, the account_history_plugin must be loaded fore these objects to be maintained.
    this object is READ ONLY it can never be modified 

.. code-block:: cpp 

   class operation_history_object : public abstract_object<operation_history_object>
   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id  = operation_history_object_type;

         operation_history_object( const operation& o ):op(o){}
         operation_history_object(){}

         operation         op;
         operation_result  result;
         /** the block that caused this operation */
         uint32_t          block_num = 0;
         /** the transaction in the block */
         uint16_t          trx_in_block = 0;
         /** the operation within the transaction */
         uint16_t          op_in_trx = 0;
         /** any virtual operations implied by operation in block */
         uint16_t          virtual_op = 0;
   };


Order (*market*)
=======================

`call_order_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1call__order__object.html>`_ 
-----------------------------------------------------
- tracks debt and call price information
- There should only be one call_order_object per asset pair per account and they will all have the same call price. 

.. code-block:: cpp 

	class call_order_object : public abstract_object<call_order_object>
	{
	   public:
		  static const uint8_t space_id = protocol_ids;
		  static const uint8_t type_id  = call_order_object_type;

		  asset get_collateral()const { return asset( collateral, call_price.base.asset_id ); }
		  asset get_debt()const { return asset( debt, debt_type() ); }
		  asset amount_to_receive()const { return get_debt(); }
		  asset_id_type debt_type()const { return call_price.quote.asset_id; }
		  asset_id_type collateral_type()const { return call_price.base.asset_id; }
		  price collateralization()const { return get_collateral() / get_debt(); }

		  account_id_type  borrower;
		  share_type       collateral;  ///< call_price.base.asset_id, access via get_collateral
		  share_type       debt;        ///< call_price.quote.asset_id, access via get_debt
		  price            call_price;  ///< Collateral / Debt

		  optional<uint16_t> target_collateral_ratio; ///< maximum CR to maintain when selling collateral on margin call

		  pair<asset_id_type,asset_id_type> get_market()const
		  {
			 auto tmp = std::make_pair( call_price.base.asset_id, call_price.quote.asset_id );
			 if( tmp.first > tmp.second ) std::swap( tmp.first, tmp.second );
			 return tmp;
		  }

		  /// Calculate maximum quantity of debt to cover to satisfy @ref target_collateral_ratio.
		  share_type get_max_debt_to_cover( price match_price, price feed_price, const uint16_t maintenance_collateral_ratio )const;
	};



`collateral_bid_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1collateral__bid__object.html>`_ 
-----------------------------------------------------
- bids of collateral for debt after a black swan
- There should only be one collateral_bid_object per asset per account, and only for smartcoin assets that have a global settlement_price. 

.. code-block:: cpp 

	class collateral_bid_object : public abstract_object<collateral_bid_object>
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id  = impl_collateral_bid_object_type;

		  asset get_additional_collateral()const { return inv_swan_price.base; }
		  asset get_debt_covered()const { return inv_swan_price.quote; }
		  asset_id_type debt_type()const { return inv_swan_price.quote.asset_id; }

		  account_id_type  bidder;
		  price            inv_swan_price;  // Collateral / Debt
	};


`force_settlement_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1force__settlement__object.html>`_ 
-----------------------------------------------------
- tracks bitassets scheduled for force settlement at some point in the future.
- On the settlement_date the balance will be converted to the collateral asset and paid to owner and then this object will be deleted. 

.. code-block:: cpp

	class force_settlement_object : public abstract_object<force_settlement_object>
	{
	   public:
		  static const uint8_t space_id = protocol_ids;
		  static const uint8_t type_id  = force_settlement_object_type;

		  account_id_type   owner;
		  asset             balance;
		  time_point_sec    settlement_date;

		  asset_id_type settlement_asset_id()const
		  { return balance.asset_id; }
	};


`limit_order_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1limit__order__object.html>`_ 
-----------------------------------------------------
- an offer to sell a amount of a asset at a specified exchange rate by a certain time
- This limit_order_objects are indexed by expiration and is automatically deleted on the first block after expiration

.. code-block:: cpp

	class limit_order_object : public abstract_object<limit_order_object>
	{
	   public:
		  static const uint8_t space_id = protocol_ids;
		  static const uint8_t type_id  = limit_order_object_type;

		  time_point_sec   expiration;
		  account_id_type  seller;
		  share_type       for_sale; ///< asset id is sell_price.base.asset_id
		  price            sell_price;
		  share_type       deferred_fee; ///< fee converted to CORE
		  asset            deferred_paid_fee; ///< originally paid fee

		  pair<asset_id_type,asset_id_type> get_market()const
		  {
			 auto tmp = std::make_pair( sell_price.base.asset_id, sell_price.quote.asset_id );
			 if( tmp.first > tmp.second ) std::swap( tmp.first, tmp.second );
			 return tmp;
		  }

		  asset amount_for_sale()const   { return asset( for_sale, sell_price.base.asset_id ); }
		  asset amount_to_receive()const { return amount_for_sale() * sell_price; }
		  asset_id_type sell_asset_id()const    { return sell_price.base.asset_id;  }
		  asset_id_type receive_asset_id()const { return sell_price.quote.asset_id; }
	};



Proposal 
==========================

`proposal_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1proposal__object.html>`_ 
-----------------------------------------------------
- tracks the approval of a partially approved transaction 	  

.. code-block:: cpp

	class proposal_object : public abstract_object<proposal_object>
	{
	   public:
		  static const uint8_t space_id = protocol_ids;
		  static const uint8_t type_id = proposal_object_type;

		  time_point_sec                expiration_time;
		  optional<time_point_sec>      review_period_time;
		  transaction                   proposed_transaction;
		  flat_set<account_id_type>     required_active_approvals;
		  flat_set<account_id_type>     available_active_approvals;
		  flat_set<account_id_type>     required_owner_approvals;
		  flat_set<account_id_type>     available_owner_approvals;
		  flat_set<public_key_type>     available_key_approvals;
		  account_id_type               proposer;
		  std::string                   fail_reason;

		  bool is_authorized_to_execute(database& db) const;
	};


Transaction 
=============================


`account_transaction_history_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1account__transaction__history__object.html>`_ 
-----------------------------------------------------
- a node in a linked list of operation_history_objects
- Account history is important for users and wallets even though it is not part of "core validation". Account history is maintained as a linked list stored on disk in a stack. Each account will point to the most recent account history object by ID. When a new operation relativent to that account is processed a new account history object is allocated at the end of the stack and initialized to point to the prior object.
- This data is never accessed as part of chain validation and therefore can be kept on disk as a memory mapped file. Using a memory mapped file will help the operating system better manage / cache / page files and also accelerates load time.
- When the transaction history for a particular account is requested the linked list can be traversed with relatively efficient disk access because of the use of a memory mapped stack. 
 
.. code-block:: cpp

   class operation_history_object : public abstract_object<operation_history_object>
   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id  = operation_history_object_type;

         operation_history_object( const operation& o ):op(o){}
         operation_history_object(){}

         operation         op;
         operation_result  result;
         /** the block that caused this operation */
         uint32_t          block_num = 0;
         /** the transaction in the block */
         uint16_t          trx_in_block = 0;
         /** the operation within the transaction */
         uint16_t          op_in_trx = 0;
         /** any virtual operations implied by operation in block */
         uint16_t          virtual_op = 0;
   };
 
 
`node_property_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1node__property__object.html>`_ 
-----------------------------------------------------
- Contains per-node database configuration.
- Transactions are evaluated differently based on per-node state. Settings here may change based on whether the node is syncing or up-to-date. Or whether the node is a witness node. Or if we're processing a transaction in a witness-signed block vs. a fresh transaction from the p2p network. Or configuration-specified tradeoffs of performance/hardfork resilience vs. paranoia. 

.. code-block:: cpp

   class node_property_object
   {
      public:
         node_property_object(){}
         ~node_property_object(){}

         uint32_t skip_flags = 0;
         std::map< block_id_type, std::vector< fc::variant_object > > debug_updates;
   };
   

`transaction_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1transaction__object.html>`_ 
-----------------------------------------------------
- The purpose of this object is to enable the detection of duplicate transactions. When a transaction is included in a block a transaction_object is added. At the end of block processing all transaction_objects that have expired can be removed from the index.   

.. code-block:: cpp

	namespace graphene { namespace chain {
	   using namespace graphene::db;
	   using boost::multi_index_container;
	   using namespace boost::multi_index;
	   /**
		* The purpose of this object is to enable the detection of duplicate transactions. When a transaction is included
		* in a block a transaction_object is added. At the end of block processing all transaction_objects that have
		* expired can be removed from the index.
		*/
	   class transaction_object : public abstract_object<transaction_object>
	   {
		  public:
			 static const uint8_t space_id = implementation_ids;
			 static const uint8_t type_id  = impl_transaction_object_type;

			 signed_transaction  trx;
			 transaction_id_type trx_id;

			 time_point_sec get_expiration()const { return trx.expiration; }
	   };

	   struct by_expiration;
	   struct by_id;
	   struct by_trx_id;
	   typedef multi_index_container<
		  transaction_object,
		  indexed_by<
			 ordered_unique< tag<by_id>, member< object, object_id_type, &object::id > >,
			 hashed_unique< tag<by_trx_id>, BOOST_MULTI_INDEX_MEMBER(transaction_object, transaction_id_type, trx_id), std::hash<transaction_id_type> >,
			 ordered_non_unique< tag<by_expiration>, const_mem_fun<transaction_object, time_point_sec, &transaction_object::get_expiration > >
		  >
	   > transaction_multi_index_type;

	   typedef generic_index<transaction_object, transaction_multi_index_type> transaction_index;
	} }


Vesting Balance 
=============================

`vesting_balance_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1vesting__balance__object.html>`_ 
-----------------------------------------------------
- Vesting balance object is a balance that is locked by the blockchain for a period of time.    

.. code-block:: cpp

   class vesting_balance_object : public abstract_object<vesting_balance_object>
   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id = vesting_balance_object_type;

         /// Account which owns and may withdraw from this vesting balance
         account_id_type owner;
         /// Total amount remaining in this vesting balance
         /// Includes the unvested funds, and the vested funds which have not yet been withdrawn
         asset balance;
         /// The vesting policy stores details on when funds vest, and controls when they may be withdrawn
         vesting_policy policy;

         vesting_balance_object() {}

         ///@brief Deposit amount into vesting balance, requiring it to vest before withdrawal
         void deposit(const fc::time_point_sec& now, const asset& amount);
         bool is_deposit_allowed(const fc::time_point_sec& now, const asset& amount)const;

         /// @brief Deposit amount into vesting balance, making the new funds vest immediately
         void deposit_vested(const fc::time_point_sec& now, const asset& amount);
         bool is_deposit_vested_allowed(const fc::time_point_sec& now, const asset& amount)const;

         /**
          * Used to remove a vesting balance from the VBO. As well as the
          * balance field, coin_seconds_earned and
          * coin_seconds_earned_last_update fields are updated.
          *
          * The money doesn't "go" anywhere; the caller is responsible for
          * crediting it to the proper account.
          */
         void withdraw(const fc::time_point_sec& now, const asset& amount);
         bool is_withdraw_allowed(const fc::time_point_sec& now, const asset& amount)const;

         /**
          * Get amount of allowed withdrawal.
          */
         asset get_allowed_withdraw(const time_point_sec& now)const;
   };
   




Witness 
=======================

`witness_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1witness__object.html>`_ 
-----------------------------------------------------
-   

.. code-block:: cpp

   class witness_object : public abstract_object<witness_object>
   {
      public:
         static const uint8_t space_id = protocol_ids;
         static const uint8_t type_id = witness_object_type;

         account_id_type  witness_account;
         uint64_t         last_aslot = 0;
         public_key_type  signing_key;
         optional< vesting_balance_id_type > pay_vb;
         vote_id_type     vote_id;
         uint64_t         total_votes = 0;
         string           url;
         int64_t          total_missed = 0;
         uint32_t         last_confirmed_block_num = 0;

         witness_object() : vote_id(vote_id_type::witness) {}
   };
   
`witness_schedule_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1witness__schedule__object.html>`_ 
-----------------------------------------------------
-   

.. code-block:: cpp

	class witness_schedule_object : public graphene::db::abstract_object<witness_schedule_object>
	{
	   public:
		  static const uint8_t space_id = implementation_ids;
		  static const uint8_t type_id = impl_witness_schedule_object_type;

		  vector< witness_id_type > current_shuffled_witnesses;
	};

Worker 
==========================

`worker_object <https://bitshares.org/doxygen/classgraphene_1_1chain_1_1worker__object.html>`_ 
-----------------------------------------------------
- Worker object contains the details of a blockchain worker. See `The Blockchain Worker System <https://bitshares.org/doxygen/group__workers.html>`_ for details.

.. code-block:: cpp

	class worker_object : public abstract_object<worker_object>
	{
	   public:
		  static const uint8_t space_id = protocol_ids;
		  static const uint8_t type_id =  worker_object_type;

		  /// ID of the account which owns this worker
		  account_id_type worker_account;
		  /// Time at which this worker begins receiving pay, if elected
		  time_point_sec work_begin_date;
		  /// Time at which this worker will cease to receive pay. Worker will be deleted at this time
		  time_point_sec work_end_date;
		  /// Amount in CORE this worker will be paid each day
		  share_type daily_pay;
		  /// ID of this worker's pay balance
		  worker_type worker;
		  /// Human-readable name for the worker
		  string name;
		  /// URL to a web page representing this worker
		  string url;

		  /// Voting ID which represents approval of this worker
		  vote_id_type vote_for;
		  /// Voting ID which represents disapproval of this worker
		  vote_id_type vote_against;

		  uint64_t total_votes_for = 0;
		  uint64_t total_votes_against = 0;

		  bool is_active(fc::time_point_sec now)const {
			 return now >= work_begin_date && now <= work_end_date;
		  }

		  share_type approving_stake()const {
			 return int64_t( total_votes_for ) - int64_t( total_votes_against );
		  }
	};



------------------------------

|

