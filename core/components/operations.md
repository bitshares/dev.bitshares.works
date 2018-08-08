#### BitShares Core - graphene::chain

##  graphene::chain::base_operation

Learning BitShares-Core Available Operations.

#### base_operations and Detailed Descriptions

- [account_create_operation](../components/operations.md#account_create_operation)
- [account_transfer_operation](../components/operations.md#account_transfer_operation)
- [account_update_operation](../components/operations.md#account_update_operation)
- [account_upgrade_operation](../components/operations.md#account_upgrade_operation)
- [account_whitelist_operation](../components/operations.md#account_whitelist_operation)
- [asset_operation](../components/operations.md#asset_operation)
- [asset_clain_fees_operation](../components/operations.md#asset_clain_fees_operation)
- [asset_claim_pool_operation](../components/operations.md#asset_claim_pool_operation)
- [asset_create_operation](../components/operations.md#asset_create_operation)
- [asset_fund_fee_pool_operation](../components/operations.md#asset_fund_fee_pool_operation)
- [asset_global_settle_operation](../components/operations.md#asset_global_settle_operation)
- [asset_issue_operation](../components/operations.md#asset_issue_operation)
- [asset_publish_feed_operation](../components/operations.md#asset_publish_feed_operat)
- [asset_reserve_operation](../components/operations.md#asset_reserve_operation)
- [asset_settle_cancel_operation](../components/operations.md#asset_settle_cancel_operation)
- [asset_settle_operation](../components/operations.md#asset_settle_operation)
- [asset_update_bitasset_operation](../components/operations.md#asset_update_bitasset_operation)
- [asset_update_feed_producers_operation](../components/operations.md#asset_update_feed_producers_operation)
- [asset_update_issuer_operation](../components/operations.md#asset_update_issuer_operation)
- [asset_update_operation](../components/operations.md#asset_update_operation)
- [balance_claim_operation](../components/operations.md#balance_claim_operation)
- [bit_collateral_operation](../components/operations.md#bit_collateral_operation)
- [blind_transfer_operation](../components/operations.md#blind_transfer_operation)
- [call_order_update_operation](../components/operations.md#call_order_update_operation)
- [committee_member_create_operation](../components/operations.md#committee_member_create_operation)
- [committee_member_update_global_parameters_operation](../components/operations.md#committee_member_update_global_parameters_operation)
- [committee_member_update_operation](../components/operations.md#committee_member_update_operation)
- [custom_operation](../components/operations.md#custom_operation)
- [execute_bit_operation](../components/operations.md#execute_bit_operation)
- [fba_distribute_operation](../components/operations.md#fba_distribute_operation)
- [fill_order_operation](../components/operations.md#fill_order_operation)
- [limit_order_cancel_operation](../components/operations.md#limit_order_cancel_operation)
- [limit_orders_create_operation](../components/operations.md#limit_orders_create_operation)
- [override_transfer_operation](../components/operations.md#override_transfer_operation)
- [proposal_create_operation](../components/operations.md#proposal_create_operation)
- [proposal_delete_operation](../components/operations.md#proposal_delete_operation)
- [proposal_update_operation](../components/operations.md#proposal_update_operation)
- [transfer_from_blind_operation](../components/operations.md#transfer_from_blind_operation)
- [transfer_operation](../components/operations.md#transfer_operation)
- [transfer_to_blind_operation](../components/operations.md#transfer_to_blind_operation)
- [vesting_balance_create_operation](../components/operations.md#vesting_balance_create_operation)
- [vesting_balance_withdraw_operation](../components/operations.md#vesting_balance_withdraw_operation)
- [withdraw_permission_claim_operation](../components/operations.md#withdraw_permission_claim_operation)
- [withdraw_permission_create_operation](../components/operations.md#withdraw_permission_create_operation)
- [withdraw_permission_delete_operation](../components/operations.md#withdraw_permission_delete_operation)
- [withdraw_permission_update_operation](../components/operations.md#withdraw_permission_update_operation)
- [witness_create_operation](../components/operations.md#witness_create_operation)
- [witness_update_operation](../components/operations.md#witness_update_operation)
- [worker_create_operation](../components/operations.md#worker_create_operation)


***

## Detailed Descriptions

### account_create_operation

	  struct account_create_operation : public base_operation
	  {
	  struct ext
	  {
	  optional< void_t > null_ext;
	  optional< special_authority > owner_special_authority;
	  optional< special_authority > active_special_authority;
	  optional< buyback_account_options > buyback_options;
	  };

	  struct fee_parameters_type
	  {
	  uint64_t basic_fee = 5*GRAPHENE_BLOCKCHAIN_PRECISION; 
	  uint64_t premium_fee = 2000*GRAPHENE_BLOCKCHAIN_PRECISION; 
	  uint32_t price_per_kbyte = GRAPHENE_BLOCKCHAIN_PRECISION;
	  }; 

	  asset fee;
	  account_id_type registrar;

	  account_id_type referrer;
	  uint16_t referrer_percent = 0;

	  string name;
	  authority owner;
	  authority active;

	  account_options options;
	  extension< ext > extensions;

	  account_id_type fee_payer()const { return registrar; }
	  void validate()const;
	  share_type calculate_fee(const fee_parameters_type& )const;

	  void get_required_active_authorities( flat_set<account_id_type>& a )const
	  {
	  // registrar should be required anyway as it is the fee_payer(), but we insert it here just to be sure
	  a.insert( registrar );
	  if( extensions.value.buyback_options.valid() )
	  a.insert( extensions.value.buyback_options->asset_to_buy_issuer );
	  }
	  };

### account_transfer_operation
- Transfers the account to another account while clearing the white list. 
- In theory an account can be transferred by simply updating the authorities, but that kind of transfer lacks semantic meaning and is more often done to rotate keys without transferring ownership. This operation is used to indicate the legal transfer of title to this account and a break in the operation history. 
- The account_id's owner/active/voting/memo authority should be set to new_owner
- This operation is used to indicate the legal transfer of title to this account and a break in the operation history.
- This operation will clear the account's whitelist statuses, but not the blacklist statuses. 

  	  struct account_transfer_operation : public base_operation
	  {
	  struct fee_parameters_type { uint64_t fee = 500 * GRAPHENE_BLOCKCHAIN_PRECISION; };
	 
	  asset fee;
	  account_id_type account_id;
	  account_id_type new_owner;
	  extensions_type extensions;
	 
	  account_id_type fee_payer()const { return account_id; }
	  void validate()const;
	  };

### account_update_operation
- Update an existing account.
- This operation is used to update an existing account. It can be used to update the authorities, or adjust the options on the account. See `account_object::options_type` for the options which may be updated. 

		struct account_update_operation : public base_operation
		{
		struct ext
		{
		optional< void_t > null_ext;
		optional< special_authority > owner_special_authority;
		optional< special_authority > active_special_authority;
		};

		struct fee_parameters_type
		{
		share_type fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
		uint32_t price_per_kbyte = GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		account_id_type account;

		optional<authority> owner;
		optional<authority> active;

		optional<account_options> new_options;
		extension< ext > extensions;

		account_id_type fee_payer()const { return account; }
		void validate()const;
		share_type calculate_fee( const fee_parameters_type& k )const;

		bool is_owner_update()const
		{ return owner || extensions.value.owner_special_authority.valid(); }

		void get_required_owner_authorities( flat_set<account_id_type>& a )const
		{ if( is_owner_update() ) a.insert( account ); }

		void get_required_active_authorities( flat_set<account_id_type>& a )const
		{ if( !is_owner_update() ) a.insert( account ); }
		};

#### account_upgrade_operation
- Manage an account's membership status
- This operation is used to upgrade an account to a member, or renew its subscription. If an account which is an unexpired annual subscription member publishes this operation with `upgrade_to_lifetime_member` set to false, the account's membership expiration date will be pushed backward one year. If a basic account publishes it with `upgrade_to_lifetime_member` set to false, the account will be upgraded to a subscription member with an expiration date one year after the processing time of this operation.
- Any account may use this operation to become a lifetime member by setting `upgrade_to_lifetime_member` to true. Once an account has become a lifetime member, it may not use this operation anymore. 

		struct account_upgrade_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t membership_annual_fee = 2000 * GRAPHENE_BLOCKCHAIN_PRECISION;
		uint64_t membership_lifetime_fee = 10000 * GRAPHENE_BLOCKCHAIN_PRECISION; 
		};

		asset fee;
		account_id_type account_to_upgrade;
		bool upgrade_to_lifetime_member = false;
		extensions_type extensions;

		account_id_type fee_payer()const { return account_to_upgrade; }
		void validate()const;
		share_type calculate_fee( const fee_parameters_type& k )const;
		};

#### account_whitelist_operation
- This operation is used to whitelist and blacklist accounts, primarily for transacting in whitelisted assets.
- Accounts can freely specify opinions about other accounts, in the form of either whitelisting or blacklisting them. This information is used in chain validation only to determine whether an account is authorized to transact in an asset type which enforces a whitelist, but third parties can use this information for other uses as well, as long as it does not conflict with the use of whitelisted assets.
- An asset which enforces a whitelist specifies a list of accounts to maintain its whitelist, and a list of accounts to maintain its blacklist. In order for a given account A to hold and transact in a whitelisted asset S, A must be whitelisted by at least one of S's whitelist_authorities and blacklisted by none of S's blacklist_authorities. If A receives a balance of S, and is later removed from the whitelist(s) which allowed it to hold S, or added to any blacklist S specifies as authoritative, A's balance of S will be frozen until A's authorization is reinstated.
- This operation requires authorizing_account's signature, but not account_to_list's. The fee is paid by authorizing_account

		  struct account_whitelist_operation : public base_operation
		  {
		  struct fee_parameters_type { share_type fee = 300000; };
		  enum account_listing {
		  no_listing = 0x0, 
		  white_listed = 0x1, 
		  black_listed = 0x2, 
		  white_and_black_listed = white_listed | black_listed 
		  };
		 
		  asset fee;
		  account_id_type authorizing_account;
		  account_id_type account_to_list;
		  uint8_t new_listing = no_listing;
		  extensions_type extensions;
		 
		  account_id_type fee_payer()const { return authorizing_account; }
		  void validate()const { FC_ASSERT( fee.amount >= 0 ); FC_ASSERT(new_listing < 0x4); }
		  };
		 
		 

#### asset_operation
- assert that some conditions are true.
- This operation performs no changes to the database state, but can used to verify pre or post conditions for other operations. 

		struct assert_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type fee_paying_account;
		vector<predicate> predicates;
		flat_set<account_id_type> required_auths;
		extensions_type extensions;

		account_id_type fee_payer()const { return fee_paying_account; }
		void validate()const;
		share_type calculate_fee(const fee_parameters_type& k)const;
		};

#### asset_claim_fees_operation
- used to transfer accumulated fees back to the issuer's balance. 

		struct asset_claim_fees_operation : public base_operation
		{
		struct fee_parameters_type {
		uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		account_id_type issuer;
		asset amount_to_claim; 
		extensions_type extensions;

		account_id_type fee_payer()const { return issuer; }
		void validate()const;
		};

#### asset_claim_pool_operation
- Transfers BTS from the fee pool of a specified asset back to the issuer's balance. 
- Parameters
  - `fee`  Payment for the operation execution
  - `issuer`  Account which will be used for transfering BTS
  - `asset_id`  Id of the asset whose fee pool is going to be drained
  - `amount_to_claim`  Amount of BTS to claim from the fee pool
  - `extensions`  Field for future expansion
- Precondition
  - `fee` must be paid in the asset other than the one whose pool is being drained 
  - `amount_to_claim` should be specified in the core asset 
  - `amount_to_claim` should be nonnegative 
 
		struct asset_claim_pool_operation : public base_operation
		{
		struct fee_parameters_type {
		uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		account_id_type issuer;
		asset_id_type asset_id; 
		asset amount_to_claim; 
		extensions_type extensions;

		account_id_type fee_payer()const { return issuer; }
		void validate()const;
		};
  
#### asset_create_operation
 
		struct asset_create_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t symbol3 = 500000 * GRAPHENE_BLOCKCHAIN_PRECISION;
		uint64_t symbol4 = 300000 * GRAPHENE_BLOCKCHAIN_PRECISION;
		uint64_t long_symbol = 5000 * GRAPHENE_BLOCKCHAIN_PRECISION;
		uint32_t price_per_kbyte = 10; 
		};

#### asset_fund_fee_pool_operation

		struct asset_fund_fee_pool_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee; 
		account_id_type from_account;
		asset_id_type asset_id;
		share_type amount; 
		extensions_type extensions;

		account_id_type fee_payer()const { return from_account; }
		void validate()const;
		};

#### asset_global_settle_operation
- Allows global settling of bitassets (black swan or prediction markets)
- In order to use this operation, `asset_to_settle` must have the global_settle flag set
- When this operation is executed all balances are converted into the backing asset at the settle_price and all open margin positions are called at the settle price. If this asset is used as backing for other bitassets, those bitassets will be force settled at their current feed price. 

		struct asset_global_settle_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 500 * GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type issuer; 
		asset_id_type asset_to_settle;
		price settle_price;
		extensions_type extensions;

		account_id_type fee_payer()const { return issuer; }
		void validate()const;
		};

#### asset_issue_operation

		struct asset_issue_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION; 
		uint32_t price_per_kbyte = GRAPHENE_BLOCKCHAIN_PRECISION;
		};

#### asset_publish_feed_operation
- Publish price feeds for market-issued assets
- Price feed providers use this operation to publish their price feeds for **market-issued assets**. A price feed is used to tune the market for a particular **market-issued asset**. For each value in the feed, the median across all committee_member feeds for that asset is calculated and the market for the asset is configured with the median of that value.
- The feed in the operation contains three prices: **a call price limit**, **a short price limit**, and **a settlement price**. The call limit price is structured as `(collateral asset) / (debt asset)` and the short limit price is structured as `(asset for sale) / (collateral asset`). Note that the `asset IDs` are opposite to eachother, so if we're publishing a feed for USD, the call limit price will be `CORE/USD `and the short limit price will be `USD/CORE`. The settlement price may be flipped either direction, as long as it is a ratio between the **market-issued asset** and **its collateral**. 

		struct asset_publish_feed_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee; 
		account_id_type publisher;
		asset_id_type asset_id; 
		price_feed feed;
		extensions_type extensions;

		account_id_type fee_payer()const { return publisher; }
		void validate()const;
		};
  
#### asset_reserve_operation
- used to take an asset out of circulation, returning to the issuer

> Note: You cannot use this operation on market-issued assets. 

		  struct asset_reserve_operation : public base_operation
		  {
		  struct fee_parameters_type { uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION; };
		 
		  asset fee;
		  account_id_type payer;
		  asset amount_to_reserve;
		  extensions_type extensions;
		 
		  account_id_type fee_payer()const { return payer; }
		  void validate()const;
		  };
 
 
#### asset_settle_cancel_operation
- Virtual op generated when force settlement is canceled. 

		struct asset_settle_cancel_operation : public base_operation
		{
		struct fee_parameters_type { };

		asset fee;
		force_settlement_id_type settlement;
		account_id_type account;
		asset amount;
		extensions_type extensions;

		account_id_type fee_payer()const { return account; }
		void validate()const {
		FC_ASSERT( amount.amount > 0, "Must settle at least 1 unit" );
		}

		share_type calculate_fee(const fee_parameters_type& params)const
		{ return 0; }
		};


#### asset_settle_operation
- Schedules a *market-issued asset* for automatic settlement
- Holders of *market-issued assets* may request a forced settlement for some amount of their asset. This means that the specified sum will be locked by the chain and held for the settlement period, after which time the chain will choose a margin position holder and buy the settled asset using the margin's collateral. The price of this sale will be based on the feed price for the market-issued asset being settled. The exact settlement price will be the feed price at the time of settlement with an offset in favor of the margin position, where the offset is a blockchain parameter set in the `global_property_object`.
- The fee is paid by **account**, and **account** must authorize this operation

		struct asset_settle_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t fee = 100 * GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		account_id_type account;
		asset amount;
		extensions_type extensions;

		account_id_type fee_payer()const { return account; }
		void validate()const;
		};

#### asset_update_bitasset_operation
- Update options specific to BitAssets
- BitAssets have some options which are not relevant to other asset types. This operation is used to update those options an an existing BitAsset. 
- **Precondition**
  - `issuer` MUST be an existing account and MUST match `asset_object::issuer` on `asset_to_update` 
  - `asset_to_update` MUST be a BitAsset, i.e. `asset_object::is_market_issued()` returns true 
  - `fee` MUST be nonnegative, and `issuer` MUST have a sufficient balance to pay it 
  - `new_options` SHALL be internally consistent, as verified by `validate()` 
- **Postcondition**
  - `asset_to_update` will have BitAsset-specific options matching those of new_options 

		struct asset_update_bitasset_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 500 * GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type issuer;
		asset_id_type asset_to_update;

		bitasset_options new_options;
		extensions_type extensions;

		account_id_type fee_payer()const { return issuer; }
		void validate()const;
		};
		   

#### asset_update_feed_producers_operation
- Update the set of feed-producing accounts for a BitAsset
- BitAssets have price feeds selected by taking the median values of recommendations from a set of feed producers. This operation is used to specify which accounts may produce feeds for a given BitAsset. 
- Precondition
  - `issuer` MUST be an existing account, and MUST match `asset_object::issuer` on `asset_to_update` 
  - `issuer` MUST NOT be the committee account 
  - `asset_to_update` MUST be a BitAsset, i.e. `asset_object::is_market_issued()` returns true 
  - `fee` MUST be nonnegative, and `issuer` MUST have a sufficient balance to pay it 
  - Cardinality of `new_feed_producers` MUST NOT exceed `chain_parameters::maximum_asset_feed_publishers` 
- Postcondition
  - `asset_to_update` will have a set of feed producers matching `new_feed_producers` 
  - All valid feeds supplied by feed producers in `new_feed_producers`, which were already feed producers prior to execution of this operation, will be preserved 
		
		struct asset_update_feed_producers_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 500 * GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type issuer;
		asset_id_type asset_to_update;

		flat_set<account_id_type> new_feed_producers;
		extensions_type extensions;

		account_id_type fee_payer()const { return issuer; }
		void validate()const;
		};
		  
#### asset_update_issuer_operation
- Update issuer of an asset
- An issuer has general administrative power of an asset and in some cases also its shares issued to individuals. Thus, changing the issuer today requires the use of a separate operation that needs to be signed by the owner authority. 

		struct asset_update_issuer_operation : public base_operation
		{
		struct fee_parameters_type {
		uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		account_id_type issuer;
		asset_id_type asset_to_update;
		account_id_type new_issuer;
		extensions_type extensions;

		account_id_type fee_payer()const { return issuer; }
		void validate()const;

		void get_required_owner_authorities( flat_set<account_id_type>& a )const
		{ a.insert( issuer ); }

		void get_required_active_authorities( flat_set<account_id_type>& a )const
		{ }

		};
  
#### asset_update_operation
- Update options common to all assets
- There are a number of options which all assets in the network use. These options are enumerated in the `asset_options` struct. This operation is used to update these options for an existing asset. 

 > Note: This operation cannot be used to update BitAsset-specific options. For these options, use `asset_update_bitasset_operation` instead

- **Precondition**
  - `issuer` SHALL be an existing account and MUST match `asset_object::issuer` on `asset_to_update` 
  - `fee` SHALL be nonnegative, and `issuer` MUST have a sufficient balance to pay it 
  - `new_options` SHALL be internally consistent, as verified by `validate()` 
- **Postcondition**
  - `asset_to_update` will have options matching those of new_options 

		struct asset_update_issuer_operation : public base_operation
		{
		struct fee_parameters_type {
		uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		account_id_type issuer;
		asset_id_type asset_to_update;
		account_id_type new_issuer;
		extensions_type extensions;

		account_id_type fee_payer()const { return issuer; }
		void validate()const;

		void get_required_owner_authorities( flat_set<account_id_type>& a )const
		{ a.insert( issuer ); }

		void get_required_active_authorities( flat_set<account_id_type>& a )const
		{ }

		};
  
#### balance_claim_operation
- Claim a balance in a balanc_object.
- This operation is used to claim the balance in a given `balance_object`. If the balance object contains a vesting balance, `total_claimed` must not exceed `balance_object::available` at the time of evaluation. If the object contains a non-vesting balance, `total_claimed` must be the full balance of the object. 

#### bit_collateral_operation
- This operation can be used after a black swan to bid collateral for taking over part of the debt and the settlement_fund (see BSIP-0018).

	  struct bid_collateral_operation : public base_operation
	  {
	  struct fee_parameters_type { uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION; };
	 
	  asset fee;
	  account_id_type bidder; 
	  asset additional_collateral; 
	  asset debt_covered; 
	  extensions_type extensions;
	 
	  account_id_type fee_payer()const { return bidder; }
	  void validate()const;
	  };
  
#### blind_transfer_operation
- Transfers from blind to blind.
- There are two ways to transfer value while maintaining privacy:
  1. account to account with amount kept secret
  2. stealth transfers with amount sender/receiver kept secret
- When doing account to account transfers, everyone with access to the memo key can see the amounts, but they will not have access to the funds.
- When using stealth transfers the same key is used for control and reading the memo.
- This operation is more expensive than a normal transfer and has a fee proportional to the size of the operation.
- All assets in a blind transfer must be of the same type: fee.asset_id The fee_payer is the temp account and can be funded from the blinded values.
- Using this operation you can transfer from an account and/or blinded balances to an account and/or blinded balances.

**Stealth Transfers:**

- Assuming Receiver has key pair R,r and has shared public key R with Sender Assuming Sender has key pair S,s Generate one time key pair O,o as s.child(nonce) where nonce can be inferred from transaction Calculate secret V = o*R blinding_factor = sha256(V) memo is encrypted via aes of V owner = R.child(sha256(blinding_factor))
- Sender gives Receiver output ID to complete the payment.
- This process can also be used to send money to a cold wallet without having to pre-register any accounts.
- Outputs are assigned the same IDs as the inputs until no more input IDs are available, in which case a the return value will be the first ID allocated for an output. Additional output IDs are allocated sequentially thereafter. If there are fewer outputs than inputs then the input IDs are freed and never used again. 

		struct blind_transfer_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t fee = 5*GRAPHENE_BLOCKCHAIN_PRECISION; 
		uint32_t price_per_output = 5*GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		vector<blind_input> inputs;
		vector<blind_output> outputs;

		account_id_type fee_payer()const;
		void validate()const;
		share_type calculate_fee( const fee_parameters_type& k )const;

		void get_required_authorities( vector<authority>& a )const
		{
		for( const auto& in : inputs )
		a.push_back( in.owner ); 
		}
		};

#### call_order_update_operation
- This operation can be used to add collateral, cover, and adjust the margin call price for a particular user.
- For prediction markets the collateral and debt must always be equal.
- This operation will fail if it would trigger a margin call that couldn't be filled. If the margin call hits the call price limit then it will fail if the call price is above the settlement price.

 > Note: this operation can be used to force a market order using the collateral without requiring outside funds. 
		
		struct call_order_update_operation : public base_operation
		{
		struct options_type
		{
		optional<uint16_t> target_collateral_ratio; 
		};

		struct fee_parameters_type { uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type funding_account; 
		asset delta_collateral; 
		asset delta_debt; 

		typedef extension<options_type> extensions_type; // note: this will be jsonified to {...} but no longer [...]
		extensions_type extensions;

		account_id_type fee_payer()const { return funding_account; }
		void validate()const;
		};
  
#### committee_member_create_operation
- Create a committee_member object, as a bid to hold a committee_member seat on the network.
- Accounts which wish to become committee_members may use this operation to create a committee_member object which stakeholders may vote on to approve its position as a committee_member. 

	  struct committee_member_create_operation : public base_operation
	  {
	  struct fee_parameters_type { uint64_t fee = 5000 * GRAPHENE_BLOCKCHAIN_PRECISION; };
	 
	  asset fee;
	  account_id_type committee_member_account;
	  string url;
	 
	  account_id_type fee_payer()const { return committee_member_account; }
	  void validate()const;
	  };
	  
#### committee_member_update_global_parameters_operation
- Used by committee_members to update the global parameters of the blockchain.
- This operation allows the committee_members to update the global parameters on the blockchain. These control various tunable aspects of the chain, including block and maintenance intervals, maximum data sizes, the fees charged by the network, etc.
- This operation may only be used in a proposed transaction, and a proposed transaction which contains this operation must have a review period specified in the current global parameters before it may be accepted. 

	  struct committee_member_update_global_parameters_operation : public base_operation
	  {
	  struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };
	 
	  asset fee;
	  chain_parameters new_parameters;
	 
	  account_id_type fee_payer()const { return account_id_type(); }
	  void validate()const;
	  };
  
#### committee_member_update_operation
- Update a committee_member object.
- Currently the only field which can be updated is the url field. 

	  struct committee_member_update_operation : public base_operation
	  {
	  struct fee_parameters_type { uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION; };
	 
	  asset fee;
	  committee_member_id_type committee_member;
	  account_id_type committee_member_account;
	  optional< string > new_url;
	 
	  account_id_type fee_payer()const { return committee_member_account; }
	  void validate()const;
	  };
  
#### custom_operation
- provides a generic way to add higher level protocols on top of witness consensus
- There is no validation for this operation other than that required auths are valid and a fee is paid that is appropriate for the data contained. 

		  struct custom_operation : public base_operation
		  {
		  struct fee_parameters_type { 
		  uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; 
		  uint32_t price_per_kbyte = 10;
		  };
		 
		  asset fee;
		  account_id_type payer;
		  flat_set<account_id_type> required_auths;
		  uint16_t id = 0;
		  vector<char> data;
		 
		  account_id_type fee_payer()const { return payer; }
		  void validate()const;
		  share_type calculate_fee(const fee_parameters_type& k)const;
		  };
  
#### execute_bit_operation

 > Note: This is a virtual operation that is created while reviving a bitasset from collateral bids. 

		struct execute_bid_operation : public base_operation
		{
		struct fee_parameters_type {};

		execute_bid_operation(){}
		execute_bid_operation( account_id_type a, asset d, asset c )
		: bidder(a), debt(d), collateral(c) {}

		account_id_type bidder;
		asset debt;
		asset collateral;
		asset fee;

		account_id_type fee_payer()const { return bidder; }
		void validate()const { FC_ASSERT( !"virtual operation" ); }

		share_type calculate_fee(const fee_parameters_type& k)const { return 0; }
		};
  
#### fba_distribute_operation

		struct fba_distribute_operation : public base_operation
		{
		struct fee_parameters_type {};

		asset fee; // always zero
		account_id_type account_id;
		fba_accumulator_id_type fba_id;
		share_type amount;

		account_id_type fee_payer()const { return account_id; }
		void validate()const { FC_ASSERT( false ); }
		share_type calculate_fee(const fee_parameters_type& k)const { return 0; }
		};
	 
#### fill_order_operation

 > Note: This is a virtual operation that is created while matching orders and emitted for the purpose of accurately tracking account history, accelerating a reindex
	
		struct fill_order_operation : public base_operation
		{
		struct fee_parameters_type {};

		fill_order_operation(){}
		fill_order_operation( object_id_type o, account_id_type a, asset p, asset r, asset f, price fp, bool m )
		:order_id(o),account_id(a),pays(p),receives(r),fee(f),fill_price(fp),is_maker(m) {}

		object_id_type order_id;
		account_id_type account_id;
		asset pays;
		asset receives;
		asset fee; // paid by receiving account
		price fill_price;
		bool is_maker;

		pair<asset_id_type,asset_id_type> get_market()const
		{
		return pays.asset_id < receives.asset_id ?
		std::make_pair( pays.asset_id, receives.asset_id ) :
		std::make_pair( receives.asset_id, pays.asset_id );
		}
		account_id_type fee_payer()const { return account_id; }
		void validate()const { FC_ASSERT( !"virtual operation" ); }

		share_type calculate_fee(const fee_parameters_type& k)const { return 0; }
		};

  
  
#### limit_order_cancel_operation

- Used to cancel an existing limit order. Both fee_pay_account and the account to receive the proceeds must be the same as order->seller.
- **Returns**   the amount actually refunded 
	
		struct limit_order_cancel_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 0; };

		asset fee;
		limit_order_id_type order;
		account_id_type fee_paying_account;
		extensions_type extensions;

		account_id_type fee_payer()const { return fee_paying_account; }
		void validate()const;
		};

#### limit_orders_create_operation
- instructs the blockchain to attempt to sell one asset for another
- The blockchain will atempt to sell `amount_to_sell.asset_id` for as much `min_to_receive.asset_id` as possible. The fee will be paid by the seller's account. Market fees will apply as specified by the issuer of both the selling asset and the receiving asset as a percentage of the amount exchanged.
- If either the selling asset or the receiving asset is white list restricted, the order will only be created if the seller is on the white list of the restricted asset type.
- Market orders are matched in the order they are included in the block chain. 

		struct limit_order_create_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 5 * GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type seller;
		asset amount_to_sell;
		asset min_to_receive;

		time_point_sec expiration = time_point_sec::maximum();

		bool fill_or_kill = false;
		extensions_type extensions;

		pair<asset_id_type,asset_id_type> get_market()const
		{
		return amount_to_sell.asset_id < min_to_receive.asset_id ?
		std::make_pair(amount_to_sell.asset_id, min_to_receive.asset_id) :
		std::make_pair(min_to_receive.asset_id, amount_to_sell.asset_id);
		}
		account_id_type fee_payer()const { return seller; }
		void validate()const;
		price get_price()const { return amount_to_sell / min_to_receive; }
		};


#### override_transfer_operation
- Allows the issuer of an asset to transfer an asset from any account to any account if they have override_authority.
- **Precondition**
  - amount.asset_id->issuer == issuer 
  - issuer != from because this is pointless, use a normal transfer operation 

	  struct override_transfer_operation : public base_operation
	  {
	  struct fee_parameters_type {
	  uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
	  uint32_t price_per_kbyte = 10; 
	  };
	 
	  asset fee;
	  account_id_type issuer;
	  account_id_type from;
	  account_id_type to;
	  asset amount;
	 
	  optional<memo_data> memo;
	  extensions_type extensions;
	 
	  account_id_type fee_payer()const { return issuer; }
	  void validate()const;
	  share_type calculate_fee(const fee_parameters_type& k)const;
	  };
	  
#### proposal_create_operation
- The `proposal_create_operation` creates a transaction proposal, for use in multi-sig scenarios
- Creates a transaction proposal. The operations which compose the transaction are listed in order in proposed_ops, and expiration_time specifies the time by which the proposal must be accepted or it will fail permanently. The expiration_time cannot be farther in the future than the maximum expiration time set in the global properties object. 

		struct proposal_create_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION; 
		uint32_t price_per_kbyte = 10;
		};

		asset fee;
		account_id_type fee_paying_account;
		vector<op_wrapper> proposed_ops;
		time_point_sec expiration_time;
		optional<uint32_t> review_period_seconds;
		extensions_type extensions;

		static proposal_create_operation committee_proposal(const chain_parameters& param, fc::time_point_sec head_block_time );

		account_id_type fee_payer()const { return fee_paying_account; }
		void validate()const;
		share_type calculate_fee(const fee_parameters_type& k)const;
		};

#### proposal_delete_operation
- The `proposal_delete_operation` deletes an existing transaction proposal
- This operation allows the early veto of a proposed transaction. It may be used by any account which is a required authority on the proposed transaction, when that account's holder feels the proposal is ill-advised and he decides he will never approve of it and wishes to put an end to all discussion of the issue. Because he is a required authority, he could simply refuse to add his approval, but this would leave the topic open for debate until the proposal expires. Using this operation, he can prevent any further breath from being wasted on such an absurd proposal. 
 
		struct proposal_delete_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };

		account_id_type fee_paying_account;
		bool using_owner_authority = false;
		asset fee;
		proposal_id_type proposal;
		extensions_type extensions;

		account_id_type fee_payer()const { return fee_paying_account; }
		void validate()const;
		};

#### proposal_update_operation
- The `proposal_update_operation` updates an existing transaction proposal
- This operation allows accounts to add or revoke approval of a proposed transaction. Signatures sufficient to satisfy the authority of each account in approvals are required on the transaction containing this operation.
- If an account with a multi-signature authority is listed in `approvals_to_add`or `approvals_to_remove`, either all required signatures to satisfy that account's authority must be provided in the transaction containing this operation, or a secondary proposal must be created which contains this operation.

 > NOTE: If the proposal requires only an account's active authority, the account must not update adding its owner authority's approval. This is considered an error. An owner approval may only be added if the proposal requires the owner's authority.

- If an account's owner and active authority are both required, only the owner authority may approve. An attempt to add or remove active authority approval to such a proposal will fail. 

		struct proposal_update_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION; 
		uint32_t price_per_kbyte = 10;
		};

		account_id_type fee_paying_account;
		asset fee;
		proposal_id_type proposal;
		flat_set<account_id_type> active_approvals_to_add;
		flat_set<account_id_type> active_approvals_to_remove;
		flat_set<account_id_type> owner_approvals_to_add;
		flat_set<account_id_type> owner_approvals_to_remove;
		flat_set<public_key_type> key_approvals_to_add;
		flat_set<public_key_type> key_approvals_to_remove;
		extensions_type extensions;

		account_id_type fee_payer()const { return fee_paying_account; }
		void validate()const;
		share_type calculate_fee(const fee_parameters_type& k)const;
		void get_required_authorities( vector<authority>& )const;
		void get_required_active_authorities( flat_set<account_id_type>& )const;
		void get_required_owner_authorities( flat_set<account_id_type>& )const;
		};
  
#### transfer_from_blind_operation
- Converts blinded/stealth balance to a public account balance.

	 struct transfer_from_blind_operation : public base_operation
	 {
	  struct fee_parameters_type { 
	  uint64_t fee = 5*GRAPHENE_BLOCKCHAIN_PRECISION; 
	  };
	 
	  asset fee;
	  asset amount;
	  account_id_type to;
	  blind_factor_type blinding_factor;
	  vector<blind_input> inputs;
	 
	  account_id_type fee_payer()const { return GRAPHENE_TEMP_ACCOUNT; }
	  void validate()const;
	 
	  void get_required_authorities( vector<authority>& a )const
	  {
	  for( const auto& in : inputs )
	  a.push_back( in.owner ); 
	  }
	 };
	 
#### transfer_operation
- Transfers an amount of one asset from one account to another.
- Fees are paid by the "from" account
- **Precondition**
  - amount.amount > 0 
  - fee.amount >= 0 
  - from != to 
- **Postcondition**
  - from account's balance will be reduced by fee and amount 
  - to account's balance will be increased by amount 
- **Returns**
- n/a 

	  struct transfer_operation : public base_operation
	  {
	  struct fee_parameters_type {
	  uint64_t fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
	  uint32_t price_per_kbyte = 10 * GRAPHENE_BLOCKCHAIN_PRECISION; 
	  };
	 
	  asset fee;
	  account_id_type from;
	  account_id_type to;
	  asset amount;
	 
	  optional<memo_data> memo;
	  extensions_type extensions;
	 
	  account_id_type fee_payer()const { return from; }
	  void validate()const;
	  share_type calculate_fee(const fee_parameters_type& k)const;
	  };
  
#### transfer_to_blind_operation
- Converts public account balance to a blinded or stealth balance. 

		 struct transfer_to_blind_operation : public base_operation
		 {
		  struct fee_parameters_type { 
		  uint64_t fee = 5*GRAPHENE_BLOCKCHAIN_PRECISION; 
		  uint32_t price_per_output = 5*GRAPHENE_BLOCKCHAIN_PRECISION;
		  };
		 
		  asset fee;
		  asset amount;
		  account_id_type from;
		  blind_factor_type blinding_factor;
		  vector<blind_output> outputs;
		 
		  account_id_type fee_payer()const { return from; }
		  void validate()const;
		  share_type calculate_fee(const fee_parameters_type& )const;
		 };
	 
 
#### vesting_balance_create_operation
- Create a vesting balance.
- The chain allows a user to create a vesting balance. Normally, vesting balances are created automatically as part of cashback and worker operations. This operation allows vesting balances to be created manually as well.
- Manual creation of vesting balances can be used by a stakeholder to publicly demonstrate that they are committed to the chain. It can also be used as a building block to create transactions that function like public debt. Finally, it is useful for testing vesting balance functionality.
- **Returns**
  - ID of newly created `vesting_balance_object` 

		  struct vesting_balance_create_operation : public base_operation
		  {
		  struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };
		 
		  asset fee;
		  account_id_type creator; 
		  account_id_type owner; 
		  asset amount;
		  vesting_policy_initializer policy;
		 
		  account_id_type fee_payer()const { return creator; }
		  void validate()const
		  {
		  FC_ASSERT( fee.amount >= 0 );
		  FC_ASSERT( amount.amount > 0 );
		  }
		  };
		  
#### vesting_balance_withdraw_operation
- Withdraw from a vesting balance.
- Withdrawal from a not-completely-mature vesting balance will result in paying fees. 
- **Returns**
  - nothing 

		  struct vesting_balance_withdraw_operation : public base_operation
		  {
		  struct fee_parameters_type { uint64_t fee = 20*GRAPHENE_BLOCKCHAIN_PRECISION; };
		 
		  asset fee;
		  vesting_balance_id_type vesting_balance;
		  account_id_type owner; 
		  asset amount;
		 
		  account_id_type fee_payer()const { return owner; }
		  void validate()const
		  {
		  FC_ASSERT( fee.amount >= 0 );
		  FC_ASSERT( amount.amount > 0 );
		  }
		  };
	  
#### withdraw_permission_claim_operation
- Withdraw from an account which has published a withdrawal permission
- This operation is used to withdraw from an account which has authorized such a withdrawal. It may be executed at most once per withdrawal period for the given permission. On execution, `amount_to_withdraw` is transferred from `withdraw_from_account` to `withdraw_to_account`, assuming `amount_to_withdraw` is within the withdrawal limit. The withdrawal permission will be updated to note that the withdrawal for the current period has occurred, and further withdrawals will not be permitted until the next withdrawal period, assuming the permission has not expired. This operation may be executed at any time within the current withdrawal period.
- Fee is paid by `withdraw_to_accoun`t, which is required to authorize this operation 

		struct withdraw_permission_claim_operation : public base_operation
		{
		struct fee_parameters_type { 
		uint64_t fee = 20*GRAPHENE_BLOCKCHAIN_PRECISION; 
		uint32_t price_per_kbyte = 10;
		};

		asset fee;
		withdraw_permission_id_type withdraw_permission;
		account_id_type withdraw_from_account;
		account_id_type withdraw_to_account;
		asset amount_to_withdraw;
		optional<memo_data> memo;

		account_id_type fee_payer()const { return withdraw_to_account; }
		void validate()const;
		share_type calculate_fee(const fee_parameters_type& k)const;
		};

#### withdraw_permission_create_operation
- Create a new withdrawal permission
- This operation creates a withdrawal permission, which allows some authorized account to withdraw from an authorizing account. This operation is primarily useful for scheduling recurring payments.
- Withdrawal permissions define withdrawal periods, which is a span of time during which the authorized account may make a withdrawal. Any number of withdrawals may be made so long as the total amount withdrawn per period does not exceed the limit for any given period.
- Withdrawal permissions authorize only a specific pairing, i.e. a permission only authorizes one specified authorized account to withdraw from one specified authorizing account. Withdrawals are limited and may not exceed the withdrawal limit. The withdrawal must be made in the same asset as the limit; attempts with withdraw any other asset type will be rejected.
- The fee for this operation is paid by `withdraw_from_account`, and this account is required to authorize this operation. 

		struct withdraw_permission_create_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type withdraw_from_account;
		account_id_type authorized_account;
		asset withdrawal_limit;
		uint32_t withdrawal_period_sec = 0;
		uint32_t periods_until_expiration = 0;
		time_point_sec period_start_time;

		account_id_type fee_payer()const { return withdraw_from_account; }
		void validate()const;
		};

#### withdraw_permission_delete_operation
- Delete an existing withdrawal permission
- This operation cancels a withdrawal permission, thus preventing any future withdrawals using that permission.
- Fee is paid by `withdraw_from_account`, which is required to authorize this operation 

		struct withdraw_permission_delete_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 0; };

		asset fee;
		account_id_type withdraw_from_account;
		account_id_type authorized_account;
		withdraw_permission_id_type withdrawal_permission;

		account_id_type fee_payer()const { return withdraw_from_account; }
		void validate()const;
		};
	  
#### withdraw_permission_update_operation
- Update an existing withdraw permission
- This operation is used to update the settings for an existing withdrawal permission. The accounts to withdraw to and from may never be updated. The fields which may be updated are the withdrawal limit (both amount and asset type may be updated), the withdrawal period length, the remaining number of periods until expiration, and the starting time of the new period.
- Fee is paid by `withdraw_from_account`, which is required to authorize this operation 

		struct withdraw_permission_update_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type withdraw_from_account;
		account_id_type authorized_account;
		withdraw_permission_id_type permission_to_update;
		asset withdrawal_limit;
		uint32_t withdrawal_period_sec = 0;
		time_point_sec period_start_time;
		uint32_t periods_until_expiration = 0;

		account_id_type fee_payer()const { return withdraw_from_account; }
		void validate()const;
		};

#### witness_create_operation
- Create a witness object, as a bid to hold a witness position on the network.
- Accounts which wish to become witnesses may use this operation to create a witness object which stakeholders may vote on to approve its position as a witness. 

		struct witness_create_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 5000 * GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type witness_account;
		string url;
		public_key_type block_signing_key;

		account_id_type fee_payer()const { return witness_account; }
		void validate()const;
		};
  
#### witness_update_operation
- Update a witness object's URL and block signing key. 

		struct witness_update_operation : public base_operation
		{
		struct fee_parameters_type
		{
		share_type fee = 20 * GRAPHENE_BLOCKCHAIN_PRECISION;
		};

		asset fee;
		witness_id_type witness;
		account_id_type witness_account;
		optional< string > new_url;
		optional< public_key_type > new_signing_key;

		account_id_type fee_payer()const { return witness_account; }
		void validate()const;
		};
	  
#### worker_create_operation
- Create a new worker object. 

		struct worker_create_operation : public base_operation
		{
		struct fee_parameters_type { uint64_t fee = 5000*GRAPHENE_BLOCKCHAIN_PRECISION; };

		asset fee;
		account_id_type owner;
		time_point_sec work_begin_date;
		time_point_sec work_end_date;
		share_type daily_pay;
		string name;
		string url;
		worker_initializer initializer;

		account_id_type fee_payer()const { return owner; }
		void validate()const;
		};

***

