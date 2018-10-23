
.. _lib-protocols:

***************************************
Protocols
***************************************

*BitShares Core - graphene::chain*

This document purpose: to bring BitShares Protocols information (files) close to users.

These are a collection of the BitShares Protocols information. Sometimes it's difficult to find and read through all of them.  The original files locate: (..\\libraries\\chain\\include\\graphene\\chain\\protocol\\xxx.hpp)

----------------

.. contents:: Table of Contents
   :local:
   
------

Components and Descriptions 
============================

*graphene::chain::*


.. Note:: Operations have the :ref:`lib-operations` page to list all operations. Therefore, if a protocol includes any operations, only listed the operation's name. Please check the :ref:`lib-operations` page.


---------------------------

Base
=========

base 
---------------


- Operations
- Transactions
- A set of valid comands for mutating the globally shared state.
- An operation can be thought of like a function that will modify the global shared state of the blockchain.  The members of each struct are like function arguments and each operation can potentially generate a return value.
- Operations can be grouped into transactions (@ref transaction) to ensure that they occur in a particular order and that all operations apply successfully or no operations apply.
- Each operation is a fully defined state transition and can exist in a transaction on its own.

@section operation_design_principles Design Principles
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Operations have been carefully designed to include all of the information necessary to interpret them outside the context of the blockchain.   This means that information about current chain state is included in the operation even though it could be inferred from a subset of the data.   This makes the expected outcome of each operation well defined and easily understood without access to chain state.

@subsection balance_calculation Balance Calculation Principle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
We have stipulated that the current account balance may be entirely calculated from just the subset of operations that are relevant to that account.  There should be no need to process the entire blockchain inorder to know your account's balance.
		
@subsection fee_calculation Explicit Fee Principle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Blockchain fees can change from time to time and it is important that a signed transaction explicitly agree to the fees it will be paying.  This aids with account balance updates and ensures that the sender agreed to the fee prior to making the transaction.

@subsection defined_authority Explicit Authority
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each operation shall contain enough information to know which accounts must authorize the operation.  This principle enables authority verification to occur in a centralized, optimized, and parallel manner.

@subsection relevancy_principle Explicit Relevant Accounts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Each operation contains enough information to enumerate all accounts for which the operation should apear in its account history.  This principle enables us to easily define and enforce the @balance_calculation. This is superset of the @ref defined_authority

.. code-block:: cpp 

	struct void_result{};
	typedef fc::static_variant<void_result,object_id_type,asset> operation_result;

	struct base_operation
	{
		template<typename T>
		share_type calculate_fee(const T& params)const
		{
			return params.fee;
		}
		void get_required_authorities( vector<authority>& )const{}
		void get_required_active_authorities( flat_set<account_id_type>& )const{}
		void get_required_owner_authorities( flat_set<account_id_type>& )const{}
		void validate()const{}

		static uint64_t calculate_data_fee( uint64_t bytes, uint64_t price_per_kbyte );
	};
	

- For future expansion many structus include a single member of type extensions_type that can be changed when updating a protocol.  You can always add new types to a static_variant without breaking backward compatibility.  

::

	typedef static_variant<void_t>      future_extensions;


- A flat_set is used to make sure that only one extension of each type is added and that they are added in order.  
- **Note** static_variant compares only the type tag and not the  content

::
 
	typedef flat_set<future_extensions> extensions_type;


block
--------------------- 

*See listed :ref:`block`information section <lib-block>`

.. code-block:: cpp 

	struct block_header{  };
	struct signed_block_header : public block_header{  }
	struct signed_block : public signed_block_header{  };



address 
---------------

.. code-block:: cpp

	namespace fc { namespace ecc {
	  class  public_key; 
	  typedef fc::array<char,33>  public_key_data;
	} } // fc::ecc



::

	struct public_key_type;


- A 160 bit hash of a public key
- An address can be converted to or from a base58 string with 32 bit checksum.
- An address is calculated as ripemd160( sha512( compressed_ecc_public_key ) )
- When converted to a string, checksum calculated as the first 4 bytes ripemd160( address ) is appended to the binary address before converting to base58.


.. code-block:: cpp

	class address
	{
	  public:
		address();                                          ///< constructs empty / null address
		explicit address( const std::string& base58str );       ///< converts to binary, validates checksum
		address( const fc::ecc::public_key& pub );              ///< converts to binary
		explicit address( const fc::ecc::public_key_data& pub ); ///< converts to binary
		address( const pts_address& pub );                       ///< converts to binary
		address( const public_key_type& pubkey );

		static bool is_valid( const std::string& base58str, const std::string& prefix = GRAPHENE_ADDRESS_PREFIX );

		explicit operator std::string()const; ///< converts to base58 + checksum

		friend size_t hash_value( const address& v ) { 
		  const void* tmp = static_cast<const void*>(v.addr._hash+2);

		  const size_t* tmp2 = reinterpret_cast<const size_t*>(tmp);
		  return *tmp2;
		}
		fc::ripemd160 addr;
	};

	inline bool operator == ( const address& a, const address& b ) { return a.addr == b.addr; }
	inline bool operator != ( const address& a, const address& b ) { return a.addr != b.addr; }
	inline bool operator <  ( const address& a, const address& b ) { return a.addr <  b.addr; }


.. code-block:: cpp

	namespace fc
	{
	  void to_variant( const graphene::chain::address& var,  fc::variant& vo, uint32_t max_depth = 1 );
	  void from_variant( const fc::variant& var,  graphene::chain::address& vo, uint32_t max_depth = 1 );
	}

	namespace std
	{
	  template<>
	  struct hash<graphene::chain::address>
	  {
		public:
		size_t operator()(const graphene::chain::address &a) const
		{
		   return (uint64_t(a.addr._hash[0])<<32) | uint64_t( a.addr._hash[0] );
		}
	  };
	}


authority 
---------------

- class authority
- Identifies a weighted set of keys and accounts that must approve operations.


.. code-block:: cpp

	struct authority
	{
		authority(){}
		template<class ...Args>
		authority(uint32_t threshhold, Args... auths)
			 : weight_threshold(threshhold)
		{
			 add_authorities(auths...);
		}

		enum classification
		{
			 /** the key that is authorized to change owner, active, and voting keys */
			 owner  = 0,
			 /** the key that is able to perform normal operations */
			 active = 1,
			 key    = 2
		};
		void add_authority( const public_key_type& k, weight_type w )
		{
			 key_auths[k] = w;
		}
		void add_authority( const address& k, weight_type w )
		{
			 address_auths[k] = w;
		}
		void add_authority( account_id_type k, weight_type w )
		{
			 account_auths[k] = w;
		}
		bool is_impossible()const
		{
			 uint64_t auth_weights = 0;
			 for( const auto& item : account_auths ) auth_weights += item.second;
			 for( const auto& item : key_auths ) auth_weights += item.second;
			 for( const auto& item : address_auths ) auth_weights += item.second;
			 return auth_weights < weight_threshold;
		}

		template<typename AuthType>
		void add_authorities(AuthType k, weight_type w)
		{
			 add_authority(k, w);
		}
		template<typename AuthType, class ...Args>
		void add_authorities(AuthType k, weight_type w, Args... auths)
		{
			 add_authority(k, w);
			 add_authorities(auths...);
		}

		vector<public_key_type> get_keys() const
		{
			 vector<public_key_type> result;
			 result.reserve( key_auths.size() );
			 for( const auto& k : key_auths )
					result.push_back(k.first);
			 return result;
		}
		vector<address> get_addresses() const
		{
			 vector<address> result;
			 result.reserve( address_auths.size() );
			 for( const auto& k : address_auths )
					result.push_back(k.first);
			 return result;
		}

		friend bool operator == ( const authority& a, const authority& b )
		{
			 return (a.weight_threshold == b.weight_threshold) &&
							(a.account_auths == b.account_auths) &&
							(a.key_auths == b.key_auths) &&
							(a.address_auths == b.address_auths); 
		}
		uint32_t num_auths()const { return account_auths.size() + key_auths.size() + address_auths.size(); }
		void     clear() { account_auths.clear(); key_auths.clear(); }

		static authority null_authority()
		{
			 return authority( 1, GRAPHENE_NULL_ACCOUNT, 1 );
		}

		uint32_t                              weight_threshold = 0;
		flat_map<account_id_type,weight_type> account_auths;
		flat_map<public_key_type,weight_type> key_auths;
		/** needed for backward compatibility only */
		flat_map<address,weight_type>         address_auths;
	 };

 
	
- Add all account members of the given authority to the given flat_set.

.. code-block:: cpp
	 
	void add_authority_accounts(
	   flat_set<account_id_type>& result,
	   const authority& a
	   );


special_authority 
--------------------- 

.. code-block:: cpp

	struct no_special_authority {};

	struct top_holders_special_authority
	{
	   asset_id_type asset;
	   uint8_t       num_top_holders = 1;
	};

	typedef static_variant<
	   no_special_authority,
	   top_holders_special_authority
	   > special_authority;

	void validate_special_authority( const special_authority& auth );
	
	
.. _protocol-chain-param: 

chain_parameters 
--------------------- 

.. code-block:: cpp

	struct fee_schedule;

	typedef static_variant<>  parameter_extension; 
	struct chain_parameters
	{
		/** using a smart ref breaks the circular dependency created between operations and the fee schedule */
		smart_ref<fee_schedule> current_fees;                       ///< current schedule of fees
		uint8_t                 block_interval                      = GRAPHENE_DEFAULT_BLOCK_INTERVAL; 
		uint32_t                maintenance_interval                = GRAPHENE_DEFAULT_MAINTENANCE_INTERVAL; 
		uint8_t                 maintenance_skip_slots              = GRAPHENE_DEFAULT_MAINTENANCE_SKIP_SLOTS; 
		uint32_t                committee_proposal_review_period    = GRAPHENE_DEFAULT_COMMITTEE_PROPOSAL_REVIEW_PERIOD_SEC; 
		uint32_t                maximum_transaction_size            = GRAPHENE_DEFAULT_MAX_TRANSACTION_SIZE; 
		uint32_t                maximum_block_size                  = GRAPHENE_DEFAULT_MAX_BLOCK_SIZE; 
		uint32_t                maximum_time_until_expiration       = GRAPHENE_DEFAULT_MAX_TIME_UNTIL_EXPIRATION; 
		uint32_t                maximum_proposal_lifetime           = GRAPHENE_DEFAULT_MAX_PROPOSAL_LIFETIME_SEC; 
		uint8_t                 maximum_asset_whitelist_authorities = GRAPHENE_DEFAULT_MAX_ASSET_WHITELIST_AUTHORITIES; 
		uint8_t                 maximum_asset_feed_publishers       = GRAPHENE_DEFAULT_MAX_ASSET_FEED_PUBLISHERS; 
		uint16_t                maximum_witness_count               = GRAPHENE_DEFAULT_MAX_WITNESSES; 
		uint16_t                maximum_committee_count             = GRAPHENE_DEFAULT_MAX_COMMITTEE; 
		uint16_t                maximum_authority_membership        = GRAPHENE_DEFAULT_MAX_AUTHORITY_MEMBERSHIP; 
		uint16_t                reserve_percent_of_fee              = GRAPHENE_DEFAULT_BURN_PERCENT_OF_FEE; 
		uint16_t                network_percent_of_fee              = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE; 
		uint16_t                lifetime_referrer_percent_of_fee    = GRAPHENE_DEFAULT_LIFETIME_REFERRER_PERCENT_OF_FEE;
		uint32_t                cashback_vesting_period_seconds     = GRAPHENE_DEFAULT_CASHBACK_VESTING_PERIOD_SEC; 
		share_type              cashback_vesting_threshold          = GRAPHENE_DEFAULT_CASHBACK_VESTING_THRESHOLD; 
		bool                    count_non_member_votes              = true; ///< set to false to restrict voting privlegages to member accounts
		bool                    allow_non_member_whitelists         = false; ///< true if non-member accounts may set whitelists and blacklists; false otherwise
		share_type              witness_pay_per_block               = GRAPHENE_DEFAULT_WITNESS_PAY_PER_BLOCK; 
		uint32_t                witness_pay_vesting_seconds         = GRAPHENE_DEFAULT_WITNESS_PAY_VESTING_SECONDS; 
		share_type              worker_budget_per_day               = GRAPHENE_DEFAULT_WORKER_BUDGET_PER_DAY; 
		uint16_t                max_predicate_opcode                = GRAPHENE_DEFAULT_MAX_ASSERT_OPCODE; 
		share_type              fee_liquidation_threshold           = GRAPHENE_DEFAULT_FEE_LIQUIDATION_THRESHOLD; 
		uint16_t                accounts_per_fee_scale              = GRAPHENE_DEFAULT_ACCOUNTS_PER_FEE_SCALE; 
		uint8_t                 account_fee_scale_bitshifts         = GRAPHENE_DEFAULT_ACCOUNT_FEE_SCALE_BITSHIFTS; 
		uint8_t                 max_authority_depth                 = GRAPHENE_MAX_SIG_CHECK_DEPTH;
		extensions_type         extensions;

		/** defined in fee_schedule.cpp */
		void validate()const;
	};


**The descriptions of valuables**

:GRAPHENE_DEFAULT_BLOCK_INTERVAL:  interval in seconds between blocks   
:GRAPHENE_DEFAULT_MAINTENANCE_INTERVAL:  interval in sections between blockchain maintenance events 
:GRAPHENE_DEFAULT_MAINTENANCE_SKIP_SLOTS: number of block_intervals to skip at maintenance time 
:GRAPHENE_DEFAULT_COMMITTEE_PROPOSAL_REVIEW_PERIOD_SEC: minimum time in seconds that a proposed transaction requiring committee authority may not be signed, prior to expiration 
:GRAPHENE_DEFAULT_MAX_TRANSACTION_SIZE: maximum allowable size in bytes for a transaction
:GRAPHENE_DEFAULT_MAX_BLOCK_SIZE: aximum allowable size in bytes for a block 
:GRAPHENE_DEFAULT_MAX_TIME_UNTIL_EXPIRATION: maximum lifetime in seconds for transactions to be valid, before expiring 
:GRAPHENE_DEFAULT_MAX_PROPOSAL_LIFETIME_SEC: maximum lifetime in seconds for proposed transactions to be kept, before expiring 
:GRAPHENE_DEFAULT_MAX_ASSET_WHITELIST_AUTHORITIES: maximum number of accounts which an asset may list as authorities for its whitelist OR blacklist 
:GRAPHENE_DEFAULT_MAX_ASSET_FEED_PUBLISHERS: the maximum number of feed publishers for a given asset 
:GRAPHENE_DEFAULT_MAX_WITNESSES: maximum number of active witnesses 
:GRAPHENE_DEFAULT_MAX_COMMITTEE: aximum number of active committee_members 
:GRAPHENE_DEFAULT_MAX_AUTHORITY_MEMBERSHIP: largest number of keys/accounts an authority can have 
:GRAPHENE_DEFAULT_BURN_PERCENT_OF_FEE:   the percentage of the network's allocation of a fee that is taken out of circulation  
:GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE: percent of transaction fees paid to network 
:GRAPHENE_DEFAULT_LIFETIME_REFERRER_PERCENT_OF_FEE: percent of transaction fees paid to network 
:GRAPHENE_DEFAULT_CASHBACK_VESTING_PERIOD_SEC: time after cashback rewards are accrued before they become liquid 
:GRAPHENE_DEFAULT_CASHBACK_VESTING_THRESHOLD: the maximum cashback that can be received without vesting 
:GRAPHENE_DEFAULT_WITNESS_PAY_PER_BLOCK:   CORE to be allocated to witnesses (per block)  
:GRAPHENE_DEFAULT_WITNESS_PAY_VESTING_SECONDS: vesting_seconds parameter for witness VBO's 
:GRAPHENE_DEFAULT_WORKER_BUDGET_PER_DAY: CORE to be allocated to workers (per day) 
:GRAPHENE_DEFAULT_MAX_ASSERT_OPCODE: predicate_opcode must be less than this number 
:GRAPHENE_DEFAULT_FEE_LIQUIDATION_THRESHOLD: value in CORE at which accumulated fees in blockchain-issued market assets should be liquidated 
:GRAPHENE_DEFAULT_ACCOUNTS_PER_FEE_SCALE: number of accounts between fee scalings 
:GRAPHENE_DEFAULT_ACCOUNT_FEE_SCALE_BITSHIFTS: number of times to left bitshift account registration fee at each scaling 
:GRAPHENE_MAX_SIG_CHECK_DEPTH:    




types 
--------------------- 

.. code-block:: cpp

	using namespace graphene::db;

	using                               std::map;
	using                               std::vector;
	using                               std::unordered_map;
	using                               std::string;
	using                               std::deque;
	using                               std::shared_ptr;
	using                               std::weak_ptr;
	using                               std::unique_ptr;
	using                               std::set;
	using                               std::pair;
	using                               std::enable_shared_from_this;
	using                               std::tie;
	using                               std::make_pair;

	using                               fc::smart_ref;
	using                               fc::variant_object;
	using                               fc::variant;
	using                               fc::enum_type;
	using                               fc::optional;
	using                               fc::unsigned_int;
	using                               fc::signed_int;
	using                               fc::time_point_sec;
	using                               fc::time_point;
	using                               fc::safe;
	using                               fc::flat_map;
	using                               fc::flat_set;
	using                               fc::static_variant;
	using                               fc::ecc::range_proof_type;
	using                               fc::ecc::range_proof_info;
	using                               fc::ecc::commitment_type;
	struct void_t{};

	typedef fc::ecc::private_key        private_key_type;
	typedef fc::sha256 chain_id_type;

	typedef boost::rational< int32_t >   ratio_type;

	enum asset_issuer_permission_flags
	{
	  charge_market_fee    = 0x01, /**< an issuer-specified percentage of all market trades in this asset is paid to the issuer */
	  white_list           = 0x02, /**< accounts must be whitelisted in order to hold this asset */
	  override_authority   = 0x04, /**< issuer may transfer asset back to himself */
	  transfer_restricted  = 0x08, /**< require the issuer to be one party to every transfer */
	  disable_force_settle = 0x10, /**< disable force settling */
	  global_settle        = 0x20, /**< allow the bitasset issuer to force a global settling -- this may be set in permissions, but not flags */
	  disable_confidential = 0x40, /**< allow the asset to be used with confidential transactions */
	  witness_fed_asset    = 0x80, /**< allow the asset to be fed by witnesses */
	  committee_fed_asset  = 0x100 /**< allow the asset to be fed by the committee */
	};

	const static uint32_t ASSET_ISSUER_PERMISSION_MASK = charge_market_fee|white_list|override_authority|transfer_restricted|disable_force_settle|global_settle|disable_confidential
	  |witness_fed_asset|committee_fed_asset;
	  
	const static uint32_t UIA_ASSET_ISSUER_PERMISSION_MASK = charge_market_fee|white_list|override_authority|transfer_restricted|disable_confidential;

	enum reserved_spaces
	{
	  relative_protocol_ids = 0,
	  protocol_ids          = 1,
	  implementation_ids    = 2
	};

	inline bool is_relative( object_id_type o ){ return o.space() == 0; }
	

- List all object types from all namespaces here so they can be easily reflected and displayed in debug output.  If a 3rd party  wants to extend the core code then they will have to change the packed_object::type field from enum_type to uint16 to avoid warnings when converting packed_objects to/from json.

.. code-block:: cpp

	enum object_type
	{
	  null_object_type,
	  base_object_type,
	  account_object_type,
	  asset_object_type,
	  force_settlement_object_type,
	  committee_member_object_type,
	  witness_object_type,
	  limit_order_object_type,
	  call_order_object_type,
	  custom_object_type,
	  proposal_object_type,
	  operation_history_object_type,
	  withdraw_permission_object_type,
	  vesting_balance_object_type,
	  worker_object_type,
	  balance_object_type,
	  OBJECT_TYPE_COUNT ///< Sentry value which contains the number of different object types
	};

	enum impl_object_type
	{
	  impl_global_property_object_type,
	  impl_dynamic_global_property_object_type,
	  impl_reserved0_object_type,      // formerly index_meta_object_type, TODO: delete me
	  impl_asset_dynamic_data_type,
	  impl_asset_bitasset_data_type,
	  impl_account_balance_object_type,
	  impl_account_statistics_object_type,
	  impl_transaction_object_type,
	  impl_block_summary_object_type,
	  impl_account_transaction_history_object_type,
	  impl_blinded_balance_object_type,
	  impl_chain_property_object_type,
	  impl_witness_schedule_object_type,
	  impl_budget_record_object_type,
	  impl_special_authority_object_type,
	  impl_buyback_object_type,
	  impl_fba_accumulator_object_type,
	  impl_collateral_bid_object_type
	};


	class account_object;
	class committee_member_object;
	class witness_object;
	class asset_object;
	class force_settlement_object;
	class limit_order_object;
	class call_order_object;
	class custom_object;
	class proposal_object;
	class operation_history_object;
	class withdraw_permission_object;
	class vesting_balance_object;
	class worker_object;
	class balance_object;
	class blinded_balance_object;


	typedef object_id< protocol_ids, account_object_type,            account_object>               account_id_type;
	typedef object_id< protocol_ids, asset_object_type,              asset_object>                 asset_id_type;
	typedef object_id< protocol_ids, force_settlement_object_type,   force_settlement_object>      force_settlement_id_type;
	typedef object_id< protocol_ids, committee_member_object_type,   committee_member_object>      committee_member_id_type;
	typedef object_id< protocol_ids, witness_object_type,            witness_object>               witness_id_type;
	typedef object_id< protocol_ids, limit_order_object_type,        limit_order_object>           limit_order_id_type;
	typedef object_id< protocol_ids, call_order_object_type,         call_order_object>            call_order_id_type;
	typedef object_id< protocol_ids, custom_object_type,             custom_object>                custom_id_type;
	typedef object_id< protocol_ids, proposal_object_type,           proposal_object>              proposal_id_type;
	typedef object_id< protocol_ids, operation_history_object_type,  operation_history_object>     operation_history_id_type;
	typedef object_id< protocol_ids, withdraw_permission_object_type,withdraw_permission_object>   withdraw_permission_id_type;
	typedef object_id< protocol_ids, vesting_balance_object_type,    vesting_balance_object>       vesting_balance_id_type;
	typedef object_id< protocol_ids, worker_object_type,             worker_object>                worker_id_type;
	typedef object_id< protocol_ids, balance_object_type,            balance_object>               balance_id_type;

	// implementation types
	class global_property_object;
	class dynamic_global_property_object;
	class asset_dynamic_data_object;
	class asset_bitasset_data_object;
	class account_balance_object;
	class account_statistics_object;
	class transaction_object;
	class block_summary_object;
	class account_transaction_history_object;
	class chain_property_object;
	class witness_schedule_object;
	class budget_record_object;
	class special_authority_object;
	class buyback_object;
	class fba_accumulator_object;
	class collateral_bid_object;

	typedef object_id< implementation_ids, impl_global_property_object_type,  global_property_object>                    global_property_id_type;
	typedef object_id< implementation_ids, impl_dynamic_global_property_object_type,  dynamic_global_property_object>    dynamic_global_property_id_type;
	typedef object_id< implementation_ids, impl_asset_dynamic_data_type,      asset_dynamic_data_object>                 asset_dynamic_data_id_type;
	typedef object_id< implementation_ids, impl_asset_bitasset_data_type,     asset_bitasset_data_object>                asset_bitasset_data_id_type;
	typedef object_id< implementation_ids, impl_account_balance_object_type,  account_balance_object>                    account_balance_id_type;
	typedef object_id< implementation_ids, impl_account_statistics_object_type,account_statistics_object>                account_statistics_id_type;
	typedef object_id< implementation_ids, impl_transaction_object_type,      transaction_object>                        transaction_obj_id_type;
	typedef object_id< implementation_ids, impl_block_summary_object_type,    block_summary_object>                      block_summary_id_type;

	typedef object_id< implementation_ids,
					  impl_account_transaction_history_object_type,
					  account_transaction_history_object>       account_transaction_history_id_type;
	typedef object_id< implementation_ids, impl_chain_property_object_type,   chain_property_object>                     chain_property_id_type;
	typedef object_id< implementation_ids, impl_witness_schedule_object_type, witness_schedule_object>                   witness_schedule_id_type;
	typedef object_id< implementation_ids, impl_budget_record_object_type, budget_record_object >                        budget_record_id_type;
	typedef object_id< implementation_ids, impl_blinded_balance_object_type, blinded_balance_object >                    blinded_balance_id_type;
	typedef object_id< implementation_ids, impl_special_authority_object_type, special_authority_object >                special_authority_id_type;
	typedef object_id< implementation_ids, impl_buyback_object_type, buyback_object >                                    buyback_id_type;
	typedef object_id< implementation_ids, impl_fba_accumulator_object_type, fba_accumulator_object >                    fba_accumulator_id_type;
	typedef object_id< implementation_ids, impl_collateral_bid_object_type, collateral_bid_object >                      collateral_bid_id_type;

	typedef fc::array<char, GRAPHENE_MAX_ASSET_SYMBOL_LENGTH>    symbol_type;
	typedef fc::ripemd160                                        block_id_type;
	typedef fc::ripemd160                                        checksum_type;
	typedef fc::ripemd160                                        transaction_id_type;
	typedef fc::sha256                                           digest_type;
	typedef fc::ecc::compact_signature                           signature_type;
	typedef safe<int64_t>                                        share_type;
	typedef uint16_t                                             weight_type;

	struct public_key_type
	{
	   struct binary_key
	   {
		  binary_key() {}
		  uint32_t                 check = 0;
		  fc::ecc::public_key_data data;
	   };
	   fc::ecc::public_key_data key_data;
	   public_key_type();
	   public_key_type( const fc::ecc::public_key_data& data );
	   public_key_type( const fc::ecc::public_key& pubkey );
	   explicit public_key_type( const std::string& base58str );
	   operator fc::ecc::public_key_data() const;
	   operator fc::ecc::public_key() const;
	   explicit operator std::string() const;
	   friend bool operator == ( const public_key_type& p1, const fc::ecc::public_key& p2);
	   friend bool operator == ( const public_key_type& p1, const public_key_type& p2);
	   friend bool operator != ( const public_key_type& p1, const public_key_type& p2);
	};

	struct extended_public_key_type
	{
	  struct binary_key
	  {
		 binary_key() {}
		 uint32_t                   check = 0;
		 fc::ecc::extended_key_data data;
	  };
	  
	  fc::ecc::extended_key_data key_data;
	   
	  extended_public_key_type();
	  extended_public_key_type( const fc::ecc::extended_key_data& data );
	  extended_public_key_type( const fc::ecc::extended_public_key& extpubkey );
	  explicit extended_public_key_type( const std::string& base58str );
	  operator fc::ecc::extended_public_key() const;
	  explicit operator std::string() const;
	  friend bool operator == ( const extended_public_key_type& p1, const fc::ecc::extended_public_key& p2);
	  friend bool operator == ( const extended_public_key_type& p1, const extended_public_key_type& p2);
	  friend bool operator != ( const extended_public_key_type& p1, const extended_public_key_type& p2);
	};

	struct extended_private_key_type
	{
	  struct binary_key
	  {
		 binary_key() {}
		 uint32_t                   check = 0;
		 fc::ecc::extended_key_data data;
	  };
	  
	  fc::ecc::extended_key_data key_data;
	   
	  extended_private_key_type();
	  extended_private_key_type( const fc::ecc::extended_key_data& data );
	  extended_private_key_type( const fc::ecc::extended_private_key& extprivkey );
	  explicit extended_private_key_type( const std::string& base58str );
	  operator fc::ecc::extended_private_key() const;
	  explicit operator std::string() const;
	  friend bool operator == ( const extended_private_key_type& p1, const fc::ecc::extended_private_key& p2);
	  friend bool operator == ( const extended_private_key_type& p1, const extended_private_key_type& p2);
	  friend bool operator != ( const extended_private_key_type& p1, const extended_private_key_type& p2);
	};


.. code-block:: cpp

	namespace fc
	{
		void to_variant( const graphene::chain::public_key_type& var,  fc::variant& vo, uint32_t max_depth = 2 );
		void from_variant( const fc::variant& var,  graphene::chain::public_key_type& vo, uint32_t max_depth = 2 );
		void to_variant( const graphene::chain::extended_public_key_type& var, fc::variant& vo, uint32_t max_depth = 2 );
		void from_variant( const fc::variant& var, graphene::chain::extended_public_key_type& vo, uint32_t max_depth = 2 );
		void to_variant( const graphene::chain::extended_private_key_type& var, fc::variant& vo, uint32_t max_depth = 2 );
		void from_variant( const fc::variant& var, graphene::chain::extended_private_key_type& vo, uint32_t max_depth = 2 );
	}
	


operations 
--------------------- 

- operations
- Defines the set of valid operations as a discriminated union type.

.. code-block:: cpp

	typedef fc::static_variant<
		transfer_operation,
		limit_order_create_operation,
		limit_order_cancel_operation,
		call_order_update_operation,
		fill_order_operation,           // VIRTUAL
		account_create_operation,
		account_update_operation,
		account_whitelist_operation,
		account_upgrade_operation,
		account_transfer_operation,
		asset_create_operation,
		asset_update_operation,
		asset_update_bitasset_operation,
		asset_update_feed_producers_operation,
		asset_issue_operation,
		asset_reserve_operation,
		asset_fund_fee_pool_operation,
		asset_settle_operation,
		asset_global_settle_operation,
		asset_publish_feed_operation,
		witness_create_operation,
		witness_update_operation,
		proposal_create_operation,
		proposal_update_operation,
		proposal_delete_operation,
		withdraw_permission_create_operation,
		withdraw_permission_update_operation,
		withdraw_permission_claim_operation,
		withdraw_permission_delete_operation,
		committee_member_create_operation,
		committee_member_update_operation,
		committee_member_update_global_parameters_operation,
		vesting_balance_create_operation,
		vesting_balance_withdraw_operation,
		worker_create_operation,
		custom_operation,
		assert_operation,
		balance_claim_operation,
		override_transfer_operation,
		transfer_to_blind_operation,
		blind_transfer_operation,
		transfer_from_blind_operation,
		asset_settle_cancel_operation,  // VIRTUAL
		asset_claim_fees_operation,
		fba_distribute_operation,       // VIRTUAL
		bid_collateral_operation,
		execute_bid_operation,          // VIRTUAL
		asset_claim_pool_operation,
		asset_update_issuer_operation
		> operation;
	

- Appends required authorities to the result vector.  The authorities appended are not the same as those returned by get_required_auth 
- Return a set of required authorities for @ref op. 
	
.. code-block:: cpp

	void operation_get_required_authorities( const operation& op, 
		 flat_set<account_id_type>& active,
		 flat_set<account_id_type>& owner,
		 vector<authority>&  other );

	void operation_validate( const operation& op );
	

- necessary to support nested operations inside the `proposal_create_operation`

.. code-block:: cpp

	struct op_wrapper
	{
	  public:
	   op_wrapper(const operation& op = operation()):op(op){}
	   operation op;
	};



Account
===============


account 
---------------

.. code-block:: cpp

	bool is_valid_name( const string& s );
	bool is_cheap_name( const string& n );


- These are the fields which can be updated by the active authority.

.. code-block:: cpp

	struct account_options
	{
	  public_key_type  memo_key;
	  account_id_type  voting_account = GRAPHENE_PROXY_TO_SELF_ACCOUNT;

	  uint16_t num_witness = 0;
	  uint16_t num_committee = 0;

	  flat_set<vote_id_type>   votes;
	  extensions_type          extensions;

	  /// Whether this account is voting
	  inline bool              is_voting() const
	  {
		return ( voting_account != GRAPHENE_PROXY_TO_SELF_ACCOUNT || !votes.empty() );
	  }

	  void validate()const;
	};

**The description of `account_options` elements**

+-------------------------+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Parameter               |                    | Description                                                                                                                                                                |
+=========================+====================+============================================================================================================================================================================+
|public_key_type          | memo_key           |- The memo key is the key this account will typically use to encrypt/sign transaction memos and other non-validated account activities. This field is here                  |
|                         |                    | to prevent confusion if the active authority has zero or multiple keys in it.                                                                                              |
+-------------------------+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| account_id_type         | (**2)              | - If this field is set to an account ID other than GRAPHENE_PROXY_TO_SELF_ACCOUNT, then this account's votes will be ignored;                                              |
|                         |                    | its stake will be counted as voting for the referenced account's selected votes instead.                                                                                   |
+-------------------------+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| uint16_t                | num_witness = 0;   | - The number of active witnesses this account votes the blockchain should appoint Must not exceed the actual number of witnesses voted for in @ref votes                   |
+-------------------------+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| uint16_t                | num_committee = 0; | - The number of active committee members this account votes the blockchain should appoint Must not exceed the actual number of committee members voted for in @ref votes   |
+-------------------------+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| flat_set<vote_id_type>  |votes;              | - This is the list of vote IDs this account votes for. The weight of these votes is determined by this account's balance of core asset.                                    |
+-------------------------+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| extensions_type         | extensions;        |                                                                                                                                                                            |
+-------------------------+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

(**2:  voting_account = GRAPHENE_PROXY_TO_SELF_ACCOUNT; )

.. code-block:: cpp

	struct account_create_operation : public base_operation

	

memo 
--------------------- 


- defines the keys used to derive the shared secret

- Because account authorities and keys can change at any time, each memo must capture the specific keys used to derive the shared secret.  In order to read the cipher message you will need one of the two private keys.

*  If @ref from == @ref to and @ref from == 0 then no encryption is used, the memo is public.
*  If @ref from == @ref to and @ref from != 0 then invalid memo data

.. code-block:: cpp

	struct memo_data
	{
		public_key_type from;
		public_key_type to;
		/**
		 * 64 bit nonce format:
		 * [  8 bits | 56 bits   ]
		 * [ entropy | timestamp ]
		 * Timestamp is number of microseconds since the epoch
		 * Entropy is a byte taken from the hash of a new private key
		 *
		 * This format is not mandated or verified; it is chosen to ensure uniqueness of key-IV pairs only. This should
		 * be unique with high probability as long as the generating host has a high-resolution clock OR a strong source
		 * of entropy for generating private keys.
		 */
		uint64_t nonce = 0;
		
		/** This field contains the AES encrypted packed @ref memo_message */
		vector<char> message;

		/// @note custom_nonce is for debugging only; do not set to a nonzero value in production
		void        set_message(const fc::ecc::private_key& priv,
		const fc::ecc::public_key& pub, const string& msg, uint64_t custom_nonce = 0);

		std::string get_message(const fc::ecc::private_key& priv,
		const fc::ecc::public_key& pub)const;
	}

- defines a message and checksum to enable validation of successful decryption
- When encrypting/decrypting a checksum is required to determine whether or not decryption was successful.
 
.. code-block:: cpp

	struct memo_message
	{
		memo_message(){}
		memo_message( uint32_t checksum, const std::string& text )
		:checksum(checksum),text(text){}

		uint32_t    checksum = 0;
		std::string text;

		string  serialize() const;
		static  memo_message deserialize(const string& serial);
	};


balance 
---------------

+---+--------------------------------+
|   | (Operations)                   |
+===+================================+
|   | - balance_claim_operation      |
+---+--------------------------------+


transfer 
--------------------- 

+---+--------------------------------+
|   | (Operations)                   |
+===+================================+
|   | - transfer_operation           |
+---+--------------------------------+
|   | - override_transfer_operation  |
+---+--------------------------------+


confidential 
--------------------- 

.. code-block:: cpp

	using fc::ecc::blind_factor_type;


- stealth Stealth Transfer
- Operations related to stealth transfer of value
- Stealth Transfers enable users to maintain their finanical privacy against even though all transactions are public.  Every account has three balances:

  - 1. Public Balance - everyone can see the balance changes and the parties involved
  - 2. Blinded Balance - everyone can see who is transacting but not the amounts involved
  - 3. Stealth Balance - both the amounts and parties involved are obscured
  
- Account owners may set a flag that allows their account to receive(or not) transfers of these kinds. Asset issuers can enable or disable the use of each of these types of accounts.  
- Using the "temp account" which has no permissions required, users can transfer a stealth balance to the temp account and then use the temp account to register a new account.  In this way users can use stealth funds to create anonymous accounts with which they can perform other actions that are not compatible with blinded balances (such as market orders)

section referral_program Referral Progam
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Stealth transfers that do not specify any account id cannot pay referral fees so 100% of the transaction fee is paid to the network.

section transaction_fees Fees
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Stealth transfers can have an arbitrarylly large size and therefore the transaction fee for stealth transfers is based purley on the data size of the transaction.

- stealth
- This data is encrypted and stored in the encrypted memo portion of the blind output.
- set to the first 4 bytes of the shared secret used to encrypt the memo.  Used to verify that decryption was successful.

.. code-block:: cpp

	struct blind_memo
	{
	   account_id_type     from;
	   share_type          amount;
	   string              message;
	   uint32_t            check= 0;
	};


- stealth
- provided to maintain the invariant that all authority required by an operation is explicit in the operation.  Must match blinded_balance_id->owner

.. code-block:: cpp

	struct blind_input
	{
	   fc::ecc::commitment_type      commitment;
	   authority                      owner;
	};


-  When sending a stealth tranfer we assume users are unable to scan the full blockchain; therefore, payments require confirmation data to be passed out of band.   We assume this out-of-band channel is not secure and therefore the contents of the confirmation must be encrypted

.. code-block:: cpp

	struct stealth_confirmation
	{
	   struct memo_data
	   {
		  optional<public_key_type> from;
		  asset                     amount;
		  fc::sha256                blinding_factor;
		  fc::ecc::commitment_type  commitment;
		  uint32_t                  check = 0;
	   };
	  
	   operator string()const; /** Packs *this then encodes as base58 encoded string. */
	   
	   stealth_confirmation( const std::string& base58 ); /** Unpacks from a base58 string */
	   stealth_confirmation(){}

	   public_key_type           one_time_key;
	   optional<public_key_type> to;
	   vector<char>              encrypted_memo;
	};


- class blind_output
- Defines data required to create a new blind commitment
- stealth
- The blinded output that must be proven to be greater than 0

.. code-block:: cpp

	struct blind_output
	{
	   fc::ecc::commitment_type                commitment;   
	   range_proof_type                        range_proof;  /** only required if there is more than one blind output */
	   authority                               owner;
	   optional<stealth_confirmation>          stealth_memo;
	};
	


+---+---------------------------------+
|   | (Operations)                    |
+===+=================================+
|   | - transfer_to_blind_operation   |
+---+---------------------------------+
|   | - transfer_from_blind_operation |
+---+---------------------------------+
|   | - blind_transfer_operation      |
+---+---------------------------------+


custom 
--------------------- 

+---+--------------------------------+
|   | (Operations)                   |
+===+================================+
|   | - custom_operation             |
+---+--------------------------------+

	

Asset 
=================


assert 
---------------

- Used to verify that account_id->name is equal to the given string literal.
- Perform state-independent checks.  Verify account_name is a valid account name.

.. code-block:: cpp

	struct account_name_eq_lit_predicate
	{
		account_id_type account_id;
		string          name;
		bool   validate()const;
	};
	

- Used to verify that asset_id->symbol is equal to the given string literal.
- Perform state independent checks.  Verify symbol is a valid asset symbol.

.. code-block:: cpp

	struct asset_symbol_eq_lit_predicate
	{
		asset_id_type   asset_id;
		string          symbol;
		bool validate()const;
	};


- Used to verify that a specific block is part of the blockchain history.  This helps protect some high-value transactions to newly created IDs.
- The block ID must be within the last 2^16 blocks.

.. code-block:: cpp

	struct block_id_predicate
	{
		block_id_type id;
		bool validate()const{ return true; }
	};

- When defining predicates do not make the protocol dependent upon implementation details.


.. code-block:: cpp

	typedef static_variant<
		account_name_eq_lit_predicate,
		asset_symbol_eq_lit_predicate,
		block_id_predicate
	 > predicate;



+---+--------------------------------+
|   | (Operations)                   |
+===+================================+
|   | -  assert_operation            |
+---+--------------------------------+

asset 
---------------

.. code-block:: cpp


   extern const int64_t scaled_precision_lut[];

   struct price;

   struct asset
   {
      asset( share_type a = 0, asset_id_type id = asset_id_type() )
      :amount(a),asset_id(id){}

      share_type    amount;
      asset_id_type asset_id;

      asset& operator += ( const asset& o )
      {
         FC_ASSERT( asset_id == o.asset_id );
         amount += o.amount;
         return *this;
      }
      asset& operator -= ( const asset& o )
      {
         FC_ASSERT( asset_id == o.asset_id );
         amount -= o.amount;
         return *this;
      }
      asset operator -()const { return asset( -amount, asset_id ); }

      friend bool operator == ( const asset& a, const asset& b )
      {
         return std::tie( a.asset_id, a.amount ) == std::tie( b.asset_id, b.amount );
      }
      friend bool operator < ( const asset& a, const asset& b )
      {
         FC_ASSERT( a.asset_id == b.asset_id );
         return a.amount < b.amount;
      }
      friend inline bool operator <= ( const asset& a, const asset& b )
      {
         return !(b < a);
      }

      friend inline bool operator != ( const asset& a, const asset& b )
      {
         return !(a == b);
      }
      friend inline bool operator > ( const asset& a, const asset& b )
      {
         return (b < a);
      }
      friend inline bool operator >= ( const asset& a, const asset& b )
      {
         return !(a < b);
      }

      friend asset operator - ( const asset& a, const asset& b )
      {
         FC_ASSERT( a.asset_id == b.asset_id );
         return asset( a.amount - b.amount, a.asset_id );
      }
      friend asset operator + ( const asset& a, const asset& b )
      {
         FC_ASSERT( a.asset_id == b.asset_id );
         return asset( a.amount + b.amount, a.asset_id );
      }

      static share_type scaled_precision( uint8_t precision )
      {
         FC_ASSERT( precision < 19 );
         return scaled_precision_lut[ precision ];
      }

      asset multiply_and_round_up( const price& p )const; ///< Multiply and round up
   };


- The price struct stores asset prices in the Graphene system.
- A price is defined as a ratio between two assets, and represents a possible exchange rate between those two assets. prices are generally not stored in any simplified form, i.e. a price of (1000 CORE)/(20 USD) is perfectly normal.
- The assets within a price are labeled base and quote. Throughout the Graphene code base, the convention used is that the base asset is the asset being sold, and the quote asset is the asset being purchased, where the price is represented as base/quote, so in the example price above the seller is looking to sell CORE asset and get USD in return.

.. code-block:: cpp

   struct price
   {
      explicit price(const asset& _base = asset(), const asset& _quote = asset())
         : base(_base),quote(_quote){}

      asset base;
      asset quote;

      static price max(asset_id_type base, asset_id_type quote );
      static price min(asset_id_type base, asset_id_type quote );

      static price call_price(const asset& debt, const asset& collateral, uint16_t collateral_ratio);

      /// The unit price for an asset type A is defined to be a price such that for any asset m, m*A=m
      static price unit_price(asset_id_type a = asset_id_type()) { return price(asset(1, a), asset(1, a)); }

      price max()const { return price::max( base.asset_id, quote.asset_id ); }
      price min()const { return price::min( base.asset_id, quote.asset_id ); }

      double to_real()const { return double(base.amount.value)/double(quote.amount.value); }

      bool is_null()const;
      void validate()const;
   };

   price operator / ( const asset& base, const asset& quote );
   inline price operator~( const price& p ) { return price{p.quote,p.base}; }

   bool  operator <  ( const price& a, const price& b );
   bool  operator == ( const price& a, const price& b );

   inline bool  operator >  ( const price& a, const price& b ) { return (b < a); }
   inline bool  operator <= ( const price& a, const price& b ) { return !(b < a); }
   inline bool  operator >= ( const price& a, const price& b ) { return !(a < b); }
   inline bool  operator != ( const price& a, const price& b ) { return !(a == b); }

   asset operator *  ( const asset& a, const price& b ); ///< Multiply and round down

   price operator *  ( const price& p, const ratio_type& r );
   price operator /  ( const price& p, const ratio_type& r );

   inline price& operator *=  ( price& p, const ratio_type& r )
   { return p = p * r; }
   inline price& operator /=  ( price& p, const ratio_type& r )
   { return p = p / r; }
	

- class price_feed
- defines market parameters for margin positions
- Required maintenance collateral is defined as a fixed point number with a maximum value of 10.000 and a minimum value of 1.000.  (denominated in GRAPHENE_COLLATERAL_RATIO_DENOM)
- A black swan event occurs when value_of_collateral equals value_of_debt, to avoid a black swan a margin call is executed when value_of_debt * required_maintenance_collateral equals value_of_collateral using rate.
-  Default requirement is $1.75 of collateral per $1 of debt       
-  BlackSwan ---> SQR ---> MCR ----> SP 

.. code-block:: cpp

	struct price_feed
	{

		**Forced settlements will evaluate using this price, defined as BITASSET / COLLATERAL**
      price settlement_price;

      /// Price at which automatically exchanging this asset for CORE from fee pool occurs (used for paying fees)
      price core_exchange_rate;

      /** Fixed point between 1.000 and 10.000, implied fixed point denominator is GRAPHENE_COLLATERAL_RATIO_DENOM */
      uint16_t maintenance_collateral_ratio = GRAPHENE_DEFAULT_MAINTENANCE_COLLATERAL_RATIO;

      /** Fixed point between 1.000 and 10.000, implied fixed point denominator is GRAPHENE_COLLATERAL_RATIO_DENOM */
      uint16_t maximum_short_squeeze_ratio = GRAPHENE_DEFAULT_MAX_SHORT_SQUEEZE_RATIO;

      /**
       *  When updating a call order the following condition must be maintained:
       *
       *  debt * maintenance_price() < collateral
       *  debt * settlement_price    < debt * maintenance
       *  debt * maintenance_price() < debt * max_short_squeeze_price()
      price maintenance_price()const;
       */

      /** When selling collateral to pay off debt, the least amount of debt to receive should be
       *  min_usd = max_short_squeeze_price() * collateral
       *
       *  This is provided to ensure that a black swan cannot be trigged due to poor liquidity alone, it
       *  must be confirmed by having the max_short_squeeze_price() move below the black swan price.
       */
      price max_short_squeeze_price()const;
      ///@}

      friend bool operator == ( const price_feed& a, const price_feed& b )
      {
         return std::tie( a.settlement_price, a.maintenance_collateral_ratio, a.maximum_short_squeeze_ratio ) ==
                std::tie( b.settlement_price, b.maintenance_collateral_ratio, b.maximum_short_squeeze_ratio );
      }

      void validate() const;
      bool is_for( asset_id_type asset_id ) const;
	};
	
	

	 
asset_ops 
---------------

.. code-block:: cpp

	bool is_valid_symbol( const string& symbol );


- The asset_options struct contains options available on all assets in the network
- **Note**: Changes to this struct will break protocol compatibility

.. code-block:: cpp

	struct asset_options {
		share_type  max_supply = GRAPHENE_MAX_SHARE_SUPPLY;
		uint16_t    market_fee_percent = 0;
		share_type  max_market_fee = GRAPHENE_MAX_SHARE_SUPPLY;

		uint16_t    issuer_permissions = UIA_ASSET_ISSUER_PERMISSION_MASK;
		uint16_t    flags = 0;

		price       core_exchange_rate = price(asset(), asset(0, asset_id_type(1)));

		flat_set<account_id_type> whitelist_authorities;
		flat_set<account_id_type> blacklist_authorities;

		flat_set<asset_id_type>   whitelist_markets;
		flat_set<asset_id_type>   blacklist_markets;

		string          description;
		extensions_type extensions;

		void            validate()const;
	};
	

*The descriptions of `asset_options` elements*

:max_supply = GRAPHENE_MAX_SHARE_SUPPLY;:    The maximum supply of this asset which may exist at any given time. This can be as large as GRAPHENE_MAX_SHARE_SUPPLY 
:market_fee_percent = 0;:    When this asset is traded on the markets, this percentage of the total traded will be exacted and paid to the issuer. This is a fixed point value, representing hundredths of a percent, i.e. a value of 100 in this field means a 1% fee is charged on market trades of this asset. 
:max_market_fee = GRAPHENE_MAX_SHARE_SUPPLY;:    Market fees calculated as @ref market_fee_percent of the traded volume are capped to this value 
:issuer_permissions = UIA_ASSET_ISSUER_PERMISSION_MASK;:    The flags which the issuer has permission to update. See @ref asset_issuer_permission_flags 
:flags = 0;:    The currently active flags on this permission. See @ref asset_issuer_permission_flags 
:core_exchange_rate = price(asset(), asset(0, asset_id_type(1)));:    When a non-core asset is used to pay a fee, the blockchain must convert that asset to core asset in order to accept the fee. If this asset's fee pool is funded, the chain will automatically deposite fees in this asset to its accumulated fees, and withdraw from the fee pool the same amount as converted at the core exchange rate. 
:whitelist_authorities;:    A set of accounts which maintain whitelists to consult for this asset. If whitelist_authorities is non-empty, then only accounts in whitelist_authorities are allowed to hold, use, or transfer the asset. 
:blacklist_authorities;:    A set of accounts which maintain blacklists to consult for this asset. If flags & white_list is set, an account may only send, receive, trade, etc. in this asset if none of these accounts appears in its account_object::blacklisting_accounts field. If the account is blacklisted, it may not transact in this asset even if it is also whitelisted. 
:whitelist_markets;:     defines the assets that this asset may be traded against in the market 
:blacklist_markets;:   defines the assets that this asset may not be traded against in the market, must not overlap whitelist 
:description:    data that describes the meaning/purpose of this asset, fee will be charged proportional to size of description. 
:extensions;:   
:validate()const;:   Perform internal consistency checks. @throws fc::exception if any check fails |


- The bitasset_options struct contains configurable options available only to BitAssets.
- **Note** Changes to th is struct will break protocol compatibility

.. code-block:: cpp

	struct bitasset_options {
		uint32_t      feed_lifetime_sec = GRAPHENE_DEFAULT_PRICE_FEED_LIFETIME;
		uint8_t       minimum_feeds = 1;
		uint32_t      force_settlement_delay_sec = GRAPHENE_DEFAULT_FORCE_SETTLEMENT_DELAY;
		uint16_t      force_settlement_offset_percent = GRAPHENE_DEFAULT_FORCE_SETTLEMENT_OFFSET;
		uint16_t      maximum_force_settlement_volume = GRAPHENE_DEFAULT_FORCE_SETTLEMENT_MAX_VOLUME;
		asset_id_type   short_backing_asset;
		extensions_type extensions;

		void validate()const;
	};


*The descriptions of `bitasset_options` elements*

:feed_lifetime_sec = GRAPHENE_DEFAULT_PRICE_FEED_LIFETIME:    Time before a price feed expires 
:minimum_feeds = 1;:    Minimum number of unexpired feeds required to extract a median feed from 
:force_settlement_delay_sec = GRAPHENE_DEFAULT_FORCE_SETTLEMENT_DELAY;:    This is the delay between the time a long requests settlement and the chain evaluates the settlement 
:force_settlement_offset_percent = GRAPHENE_DEFAULT_FORCE_SETTLEMENT_OFFSET;:   This is the percent to adjust the feed price in the short's favor in the event of a forced settlement 
:maximum_force_settlement_volume = GRAPHENE_DEFAULT_FORCE_SETTLEMENT_MAX_VOLUME;:    Force settlement volume can be limited such that only a certain percentage of the total existing supply of the asset may be force-settled within any given chain maintenance interval. This field stores the percentage of the current supply which may be force settled within the current maintenance interval. If force settlements come due in an interval in which the maximum volume has already been settled, the new settlements will be enqueued and processed at the beginning of the next maintenance interval. 
:short_backing_asset;:    This specifies which asset type is used to collateralize short sales. This field may only be updated if the current supply of the asset is zero 
:extensions;:   
:validate()const;:   Perform internal consistency checks. @throws fc::exception if any check fails 


+---+------------------------------------------+
|   | (Operations)                             |
+===+==========================================+
|   | - balance_claim_operation                |
+---+------------------------------------------+
|   | - asset_create_operation                 |
+---+------------------------------------------+
|   | - asset_global_settle_operation          |
+---+------------------------------------------+
|   | - asset_settle_operation                 |
+---+------------------------------------------+
|   | - asset_settle_cancel_operation          |
+---+------------------------------------------+
|   | - sset_fund_fee_pool_operation           |
+---+------------------------------------------+
|   | - asset_update_operation                 |
+---+------------------------------------------+
|   | - asset_update_bitasset_operation        |
+---+------------------------------------------+
|   | - asset_update_feed_producers_operation  |
+---+------------------------------------------+
|   | - asset_publish_feed_operation           |
+---+------------------------------------------+
|   | - asset_issue_operation                  |
+---+------------------------------------------+
|   | - asset_reserve_operation                |
+---+------------------------------------------+
|   | - asset_claim_fees_operation             |
+---+------------------------------------------+
|   | - asset_update_issuer_operation          |
+---+------------------------------------------+
|   | - asset_claim_pool_operation             |
+---+------------------------------------------+



fba 
--------------------- 


+---+--------------------------------+
|   | (Operations)                   |
+===+================================+
|   | - fba_distribute_operation     |
+---+--------------------------------+



buyback 
--------------------- 

.. code-block:: cpp

	struct buyback_account_options
	{
	   asset_id_type             asset_to_buy;
	   account_id_type           asset_to_buy_issuer;
	   flat_set< asset_id_type > markets;
	};

**The descriptions of `buyback` elements**

+---------------------------+---------------------+--------------------------------------------------------------------------------------------------------------------------------+
|                           |                     | descriptions                                                                                                                   |
+===========================+=====================+================================================================================================================================+
| asset_id_type             | asset_to_buy        | The asset to buy.                                                                                                              |
+---------------------------+---------------------+--------------------------------------------------------------------------------------------------------------------------------+
| account_id_type           | asset_to_buy_issuer | Issuer of the asset.  Must sign the transaction, must match issuer of specified asset.                                         |
+---------------------------+---------------------+--------------------------------------------------------------------------------------------------------------------------------+
| flat_set< asset_id_type > | markets             | What assets the account is willing to buy with. <br />  Other assets will just sit there since the account has null authority. |
+---------------------------+---------------------+--------------------------------------------------------------------------------------------------------------------------------+


Transaction
=======================


market 
--------------------- 

+---+------------------------------------------+
|   | (Operations)                             |
+===+==========================================+
|   | - limit_order_create_operation           |
+---+------------------------------------------+
|   | - limit_order_cancel_operation           |
+---+------------------------------------------+
|   | - call_order_update_operation            |
+---+------------------------------------------+
|   | - fill_order_operation                   |
+---+------------------------------------------+
|   | - bid_collateral_operation               |
+---+------------------------------------------+
|   | - execute_bid_operation                  |
+---+------------------------------------------+


proposal 
--------------------- 


- `proposed_transactions`  The Graphene Transaction Proposal Protocol
- Graphene allows users to propose a transaction which requires approval of multiple accounts in order to execute.

- The user proposes a transaction using proposal_create_operation, then signatory accounts use proposal_update_operations to add or remove their approvals from this operation. When a sufficient number of approvals have been granted, the operations in the proposal are used to create a virtual transaction which is subsequently evaluated. Even if the transaction fails, the proposal will be kept until the expiration time, at which point, if sufficient approval is granted, the transaction will be evaluated a final time. This allows transactions which will not execute successfully until a given time to still be executed through the proposal mechanism. The first time the proposed transaction succeeds, the proposal will be regarded as resolved, and all future updates will be invalid.
-  The proposal system allows for arbitrarily complex or recursively nested authorities. If a recursive authority (i.e. an authority which requires approval of 'nested' authorities on other accounts) is required for a proposal, then a second proposal can be used to grant the nested authority's approval. That is, a second proposal can be created which, when sufficiently approved, adds the approval of a nested authority to the first proposal. This multiple-proposal scheme can be used to acquire approval for an arbitrarily deep authority tree. 
- **Note** that at any time, a proposal can be approved in a single transaction if sufficient signatures are available on the proposal_update_operation, as long as the authority tree to approve the proposal does not exceed the maximum recursion depth. In practice, however, it is easier to use proposals to acquire all approvals, as this leverages on-chain notification of all relevant parties that their approval is required. Off-chain multi-signature approval requires some off-chain mechanism for acquiring several signatures on a single transaction. This off-chain synchronization can be avoided using proposals.
  
  
* op_wrapper is used to get around the circular definition of operation and proposals that contain them.

.. code-block:: cpp

	struct op_wrapper;



+---+------------------------------------------+
|   | (Operations)                             |
+===+==========================================+
|   | -  proposal_create_operation             |
+---+------------------------------------------+
|   | - proposal_update_operation              |
+---+------------------------------------------+
|   | -  proposal_delete_operation             |
+---+------------------------------------------+	


transaction 
--------------------- 

- All transactions are sets of operations that must be applied atomically. Transactions must refer to a recent block that defines the context of the operation so that they assert a known binding to the object id's referenced in the transaction.
- Rather than specify a full block number, we only specify the lower 16 bits of the block number which means you can reference any block within the last 65,536 blocks which is 3.5 days with a 5 second block interval or 18 hours with a 1 second interval.
- All transactions must expire so that the network does not have to maintain a permanent record of all transactions ever published.  A transaction may not have an expiration date too far in the future because this would require keeping too much transaction history in memory.
-  The block prefix is the first 4 bytes of the block hash of the reference block number, which is the second 4 bytes of the @ref block_id_type (the first 4 bytes of the block ID are the block number)

.. Note:: A transaction which selects a reference block cannot be migrated between forks outside the period of ref_block_num.time to (ref_block_num.time + rel_exp * interval). This fact can be used to protect market orders which should specify a relatively short re-org window of perhaps less than 1 minute. Normal payments should probably have a longer re-org window to ensure their transaction can still go through in the event of a momentary disruption in service.


.. Note:: It is not recommended to set the @ref ref_block_num, @ref ref_block_prefix, and @ref expiration fields manually. Call the appropriate overload of @ref set_expiration instead.
   

**groups operations that should be applied atomically**

.. code-block:: cpp

	struct transaction
	{
	  /**
	   * Least significant 16 bits from the reference block number. If @ref relative_expiration is zero, this field
	   * must be zero as well.
	   */
	  uint16_t           ref_block_num    = 0;
	  /**
	   * The first non-block-number 32-bits of the reference block ID. Recall that block IDs have 32 bits of block
	   * number followed by the actual block hash, so this field should be set using the second 32 bits in the
	   * @ref block_id_type
	   */
	  uint32_t           ref_block_prefix = 0;

	  /**
	   * This field specifies the absolute expiration for this transaction.
	   */
	  fc::time_point_sec expiration;

	  vector<operation>  operations;
	  extensions_type    extensions;

	  /// Calculate the digest for a transaction
	  digest_type         digest()const;
	  transaction_id_type id()const;
	  void                validate() const;
	  /// Calculate the digest used for signature validation
	  digest_type         sig_digest( const chain_id_type& chain_id )const;

	  void set_expiration( fc::time_point_sec expiration_time );
	  void set_reference_block( const block_id_type& reference_block );

	  /// visit all operations
	  template<typename Visitor>
	  vector<typename Visitor::result_type> visit( Visitor&& visitor )
	  {
		 vector<typename Visitor::result_type> results;
		 for( auto& op : operations )
			results.push_back(op.visit( std::forward<Visitor>( visitor ) ));
		 return results;
	  }
	  template<typename Visitor>
	  vector<typename Visitor::result_type> visit( Visitor&& visitor )const
	  {
		 vector<typename Visitor::result_type> results;
		 for( auto& op : operations )
			results.push_back(op.visit( std::forward<Visitor>( visitor ) ));
		 return results;
	  }

	  void get_required_authorities( flat_set<account_id_type>& active, flat_set<account_id_type>& owner, vector<authority>& other )const;
	};


**adds a signature to a transaction**

.. code-block:: cpp

	struct signed_transaction : public transaction
	{
	  signed_transaction( const transaction& trx = transaction() )
		 : transaction(trx){}

	  /** signs and appends to signatures */
	  const signature_type& sign( const private_key_type& key, const chain_id_type& chain_id );

	  /** returns signature but does not append */
	  signature_type sign( const private_key_type& key, const chain_id_type& chain_id )const;

	  /**
	   *  The purpose of this method is to identify some subset of
	   *  @ref available_keys that will produce sufficient signatures
	   *  for a transaction.  The result is not always a minimal set of
	   *  signatures, but any non-minimal result will still pass
	   *  validation.
	   */
	  set<public_key_type> get_required_signatures(
		 const chain_id_type& chain_id,
		 const flat_set<public_key_type>& available_keys,
		 const std::function<const authority*(account_id_type)>& get_active,
		 const std::function<const authority*(account_id_type)>& get_owner,
		 uint32_t max_recursion = GRAPHENE_MAX_SIG_CHECK_DEPTH
		 )const;

	  void verify_authority(
		 const chain_id_type& chain_id,
		 const std::function<const authority*(account_id_type)>& get_active,
		 const std::function<const authority*(account_id_type)>& get_owner,
		 uint32_t max_recursion = GRAPHENE_MAX_SIG_CHECK_DEPTH )const;

	  /**
	   * This is a slower replacement for get_required_signatures()
	   * which returns a minimal set in all cases, including
	   * some cases where get_required_signatures() returns a
	   * non-minimal set.
	   */

	  set<public_key_type> minimize_required_signatures(
		 const chain_id_type& chain_id,
		 const flat_set<public_key_type>& available_keys,
		 const std::function<const authority*(account_id_type)>& get_active,
		 const std::function<const authority*(account_id_type)>& get_owner,
		 uint32_t max_recursion = GRAPHENE_MAX_SIG_CHECK_DEPTH
		 ) const;

	  flat_set<public_key_type> get_signature_keys( const chain_id_type& chain_id )const;

	  vector<signature_type> signatures;

	  /// Removes all operations and signatures
	  void clear() { operations.clear(); signatures.clear(); }
	};

.. code-block:: cpp

	void verify_authority( const vector<operation>& ops, const flat_set<public_key_type>& sigs,
						  const std::function<const authority*(account_id_type)>& get_active,
						  const std::function<const authority*(account_id_type)>& get_owner,
						  uint32_t max_recursion = GRAPHENE_MAX_SIG_CHECK_DEPTH,
						  bool allow_committe = false,
						  const flat_set<account_id_type>& active_aprovals = flat_set<account_id_type>(),
						  const flat_set<account_id_type>& owner_approvals = flat_set<account_id_type>());
	

- captures the result of evaluating the operations contained in the transaction
- When processing a transaction some operations generate new object IDs and these IDs cannot be known until the transaction is actually included into a block.  When a block is produced these new ids are captured and included with every transaction.  The index in operation_results should correspond to the same index in operations.
- If an operation did not create any new object IDs then 0 should be returned.

.. code-block:: cpp

	struct processed_transaction : public signed_transaction
	{
	  processed_transaction( const signed_transaction& trx = signed_transaction() )
		 : signed_transaction(trx){}

	  vector<operation_result> operation_results;

	  digest_type merkle_digest()const;
	};
	


withdraw_permission 
--------------------- 

- Create a new withdrawal permission
- This operation creates a withdrawal permission, which allows some authorized account to withdraw from an authorizing account. This operation is primarily useful for scheduling recurring payments.
- Withdrawal permissions define withdrawal periods, which is a span of time during which the authorized account may make a withdrawal. Any number of withdrawals may be made so long as the total amount withdrawn per period does not exceed the limit for any given period.
- Withdrawal permissions authorize only a specific pairing, i.e. a permission only authorizes one specified authorized account to withdraw from one specified authorizing account. Withdrawals are limited and may not exceed he withdrawal limit. The withdrawal must be made in the same asset as the limit; attempts with withdraw any other asset type will be rejected.
- The fee for this operation is paid by withdraw_from_account, and this account is required to authorize this operation.


+---+------------------------------------------+
|   | (Operations)                             |
+===+==========================================+
|   | - withdraw_permission_create_operation   |
+---+------------------------------------------+
|   | - withdraw_permission_update_operation   |
+---+------------------------------------------+
|   | - withdraw_permission_claim_operation    |
+---+------------------------------------------+
|   | - withdraw_permission_delete_operation   |
+---+------------------------------------------+


Fee
==============


fee_schedule 
--------------------- 

> See: ..\libraries\chain\include\graphene\chain\protocol\fee_schedule.hpp

.. code-block:: cpp

	template<typename T> struct transform_to_fee_parameters;
	template<typename ...T>
	template<typename Operation>
	typedef transform_to_fee_parameters<operation>::type fee_parameters;

	struct transform_to_fee_parameters<fc::static_variant<T...>>

	class fee_helper 
	class fee_helper<account_create_operation> 
	class fee_helper<bid_collateral_operation> 
	class fee_helper<asset_update_issuer_operation>
	class fee_helper<asset_claim_pool_operation> 
	
 
*contains all of the parameters necessary to calculate the fee for any operation*

.. code-block:: cpp

	struct fee_schedule
	{
		fee_schedule();

		static fee_schedule get_default();

		/** Finds the appropriate fee parameter struct for the operation and then calculates the appropriate fee. */
		asset calculate_fee( const operation& op, const price& core_exchange_rate = price::unit_price() )const;
		asset set_fee( operation& op, const price& core_exchange_rate = price::unit_price() )const;

		void zero_all_fees();

		/** Validates all of the parameters are present and accounted for.*/
		void validate()const;

		template<typename Operation>
		const typename Operation::fee_parameters_type& get()const
		{
		  return fee_helper<Operation>().cget(parameters);
		}
		template<typename Operation>
		typename Operation::fee_parameters_type& get()
		{
		  return fee_helper<Operation>().get(parameters);
		}

		/** @note must be sorted by fee_parameters.which() and have no duplicates */
		flat_set<fee_parameters> parameters;
		uint32_t                 scale = GRAPHENE_100_PERCENT;    ///< fee * scale / GRAPHENE_100_PERCENT
	};

	typedef fee_schedule fee_schedule_type;


	

Vote
====================


vesting 
--------------------- 

.. code-block:: cpp

	struct linear_vesting_policy_initializer
	{
	  /** while vesting begins on begin_timestamp, none may be claimed before vesting_cliff_seconds have passed */
	  fc::time_point_sec begin_timestamp;
	  uint32_t           vesting_cliff_seconds = 0;
	  uint32_t           vesting_duration_seconds = 0;
	};

	struct cdd_vesting_policy_initializer
	{
	  /** while coindays may accrue over time, none may be claimed before the start_claim time */
	  fc::time_point_sec start_claim;
	  uint32_t           vesting_seconds = 0;
	  cdd_vesting_policy_initializer( uint32_t vest_sec = 0, fc::time_point_sec sc = fc::time_point_sec() ):start_claim(sc),vesting_seconds(vest_sec){}
	};

	typedef fc::static_variant<linear_vesting_policy_initializer, cdd_vesting_policy_initializer> vesting_policy_initializer;
	

+---+------------------------------------------+
|   | (Operations)                             |
+===+==========================================+
|   | - vesting_balance_create_operation       |
+---+------------------------------------------+
|   | - vesting_balance_withdraw_operation     |
+---+------------------------------------------+

vote 
--------------------- 

- An ID for some votable object
- This class stores an ID for a votable object. The ID is comprised of two fields: a type, and an instance. The type field stores which kind of object is being voted on, and the instance stores which specific object of that type is being referenced by this ID.
- A value of vote_id_type is implicitly convertible to an unsigned 32-bit integer containing only the instance. It may also be implicitly assigned from a uint32_t, which will update the instance. It may not, however, be implicitly constructed from a uint32_t, as in this case, the type would be unknown.
- On the wire, a vote_id_type is represented as a 32-bit integer with the type in the lower 8 bits and the instance in the upper 24 bits. This means that types may never exceed 8 bits, and instances may never exceed 24 bits. 
- In JSON, a vote_id_type is represented as a string "type:instance", i.e. "1:5" would be type 1 and instance 5. 
- **Note**: In the Graphene protocol, vote_id_type instances are unique across types; that is to say, if an object of type 1 has instance 4, an object of type 0 may not also have instance 4. In other words, the type is not a namespace for instances; it is only an informational field.

.. code-block:: cpp

	struct vote_id_type
	{
	   /// Lower 8 bits are type; upper 24 bits are instance
	   uint32_t content;

	   friend size_t hash_value( vote_id_type v ) { return std::hash<uint32_t>()(v.content); }
	   enum vote_type
	   {
		  committee,
		  witness,
		  worker,
		  VOTE_TYPE_COUNT
	   };

	   /// Default constructor. Sets type and instance to 0
	   vote_id_type():content(0){}
	   /// Construct this vote_id_type with provided type and instance
	   vote_id_type(vote_type type, uint32_t instance = 0)
		  : content(instance<<8 | type)
	   {}
	   /// Construct this vote_id_type from a serial string in the form "type:instance"
	   explicit vote_id_type(const std::string& serial)
	   { try {
		  auto colon = serial.find(':');
		  FC_ASSERT( colon != std::string::npos );
		  *this = vote_id_type(vote_type(std::stoul(serial.substr(0, colon))), std::stoul(serial.substr(colon+1)));
	   } FC_CAPTURE_AND_RETHROW( (serial) ) }

	   /// Set the type of this vote_id_type
	   void set_type(vote_type type)
	   {
		  content &= 0xffffff00;
		  content |= type & 0xff;
	   }
	   /// Get the type of this vote_id_type
	   vote_type type()const
	   {
		  return vote_type(content & 0xff);
	   }

	   /// Set the instance of this vote_id_type
	   void set_instance(uint32_t instance)
	   {
		  assert(instance < 0x01000000);
		  content &= 0xff;
		  content |= instance << 8;
	   }
	   /// Get the instance of this vote_id_type
	   uint32_t instance()const
	   {
		  return content >> 8;
	   }

	   vote_id_type& operator =(vote_id_type other)
	   {
		  content = other.content;
		  return *this;
	   }
	   /// Set the instance of this vote_id_type
	   vote_id_type& operator =(uint32_t instance)
	   {
		  set_instance(instance);
		  return *this;
	   }
	   /// Get the instance of this vote_id_type
	   operator uint32_t()const
	   {
		  return instance();
	   }

	   /// Convert this vote_id_type to a serial string in the form "type:instance"
	   explicit operator std::string()const
	   {
		  return std::to_string(type()) + ":" + std::to_string(instance());
	   }
	};
	

.. code-block:: cpp

	class global_property_object;

	vote_id_type get_next_vote_id( global_property_object& gpo, vote_id_type::vote_type type );
	

.. code-block:: cpp

	namespace fc
	{

	class variant;

	void to_variant( const graphene::chain::vote_id_type& var, fc::variant& vo, uint32_t max_depth = 1 );
	void from_variant( const fc::variant& var, graphene::chain::vote_id_type& vo, uint32_t max_depth = 1 );

	}
	
	
	
BTS Holders
================

committee_member 
--------------------- 

+---+--------------------------------------------------------+
|   | (Operations)                                           |
+===+========================================================+
|   | - committee_member_create_operation                    |
+---+--------------------------------------------------------+
|   | - committee_member_update_operation                    |
+---+--------------------------------------------------------+
|   | - committee_member_update_global_parameters_operation  |
+---+--------------------------------------------------------+


witness 
--------------------- 

- operations
- Create a witness object, as a bid to hold a witness position on the network.
- Accounts which wish to become witnesses may use this operation to create a witness object which stakeholders may vote on to approve its position as a witness.
	

+---+------------------------------------------+
|   | (Operations)                             |
+===+==========================================+
|   | - witness_create_operation               |
+---+------------------------------------------+
|   | - witness_update_operation               |
+---+------------------------------------------+

	
worker 
--------------------- 

- operations
- workers The Blockchain Worker System
- Graphene blockchains allow the creation of special "workers" which are elected positions paid by the blockchain for services they provide. There may be several types of workers, and the semantics of how and when they are paid are defined by the @ref worker_type_enum enumeration. All workers are elected by core stakeholder approval, by voting for or against them.
- Workers are paid from the blockchain's daily budget if their total approval (votes for - votes against) is positive, ordered from most positive approval to least, until the budget is exhausted. Payments are processed at the blockchain maintenance interval. If a worker does not have positive approval during payment processing, or if the chain's budget is exhausted before the worker is paid, that worker is simply not paid at that interval.
- Payment is not prorated based on percentage of the interval the worker was approved. If the chain attempts to pay a worker, but the budget is insufficient to cover its entire pay, the worker is paid the remaining budget funds, even though this does not fulfill his total pay. The worker will not receive extra pay to make up the difference later. Worker pay is placed in a vesting balance and vests over the number of days specified at the worker's creation.
-  Once created, a worker is immutable and will be kept by the blockchain forever.

.. code-block:: cpp

	struct vesting_balance_worker_initializer
	{
	  vesting_balance_worker_initializer(uint16_t days=0):pay_vesting_period_days(days){}
	  uint16_t pay_vesting_period_days = 0;
	};

	struct burn_worker_initializer
	{};

	struct refund_worker_initializer
	{};

	typedef static_variant< 
	  refund_worker_initializer,
	  vesting_balance_worker_initializer,
	  burn_worker_initializer > worker_initializer;
	

+---+--------------------------------+
|   | (Operations)                   |
+===+================================+
|   | - worker_create_operation      |
+---+--------------------------------+


Other
=============

config 
--------------------- 

.. code-block:: cpp

	#include <graphene/chain/config.hpp>
  - `config.hpp <https://github.com/bitshares/bitshares-core/blob/release/libraries/chain/include/graphene/chain/config.hpp>`_


ext 
--------------------- 

.. code-block:: cpp

	amespace graphene { namespace chain {

		using fc::unsigned_int;

		template< typename T >
		struct extension
		{
		   extension() {}

		   T value;
		};

		template< typename T >
		struct graphene_extension_pack_count_visitor{  };

		template< typename Stream, typename T >
		struct graphene_extension_pack_read_visitor{  };

		template< typename Stream, typename T >
		struct graphene_extension_unpack_visitor{  };
	}};
	

.. code-block:: cpp

	namespace fc {

	template< typename T >
	struct graphene_extension_from_variant_visitor
	{


	};

	template< typename T >
	void from_variant( const fc::variant& var, graphene::chain::extension<T>& value, uint32_t max_depth ){  };

	template< typename T >
	struct graphene_extension_to_variant_visitor{  };

	template< typename T >
	void to_variant( const graphene::chain::extension<T>& value, fc::variant& var, uint32_t max_depth ){  };
	

.. code-block:: cpp

	namespace raw {

	template< typename Stream, typename T >
	void pack( Stream& stream, const graphene::chain::extension<T>& value, uint32_t _max_depth=FC_PACK_MAX_DEPTH )
	{
	   FC_ASSERT( _max_depth > 0 );
	   --_max_depth;
	   graphene::chain::graphene_extension_pack_count_visitor<T> count_vtor( value.value );
	   fc::reflector<T>::visit( count_vtor );
	   fc::raw::pack( stream, unsigned_int( count_vtor.count ), _max_depth );
	   graphene::chain::graphene_extension_pack_read_visitor<Stream,T> read_vtor( stream, value.value, _max_depth );
	   fc::reflector<T>::visit( read_vtor );
	}


	template< typename Stream, typename T >
	void unpack( Stream& s, graphene::chain::extension<T>& value, uint32_t _max_depth=FC_PACK_MAX_DEPTH )
	{
	   FC_ASSERT( _max_depth > 0 );
	   --_max_depth;
	   value = graphene::chain::extension<T>();
	   graphene::chain::graphene_extension_unpack_visitor<Stream, T> vtor( s, value.value, _max_depth );
	   fc::reflector<T>::visit( vtor );
	   FC_ASSERT( vtor.count_left == 0 ); // unrecognized extension throws here
	}

	} // fc::raw
	};
	
	
-------------------------------

|

