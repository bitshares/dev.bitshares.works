
.. _trnsf-smartcontract:

Transfer - smart contract
***********************

In this section, we examine how Transfer smart contract was constructed. There are components to build the smart contract. Let's find out how transfer smart contract has been constructed. 

* Smart contract items

  - Object
  - Object Index and Call
  - Operations
  - Validations
  - Initialize Evaluators and Index
  - Evaluators
  - API call

-------------------


Smart contract items
========================


Research - transfer
-----------------------

Object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- account_object
- asset_object
- asset_dynamic_data_object



Object Index and Search Call
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- get_asset()
- get_account()

Operations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- transfer_operation 
- override_transfer_operation
- blind_transfer_operation
- transfer_from_blind_operation 
- transfer_to_blind_operation 


Validations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp 



Initialize Evaluators and Index
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp 

	void database::initialize_evaluators()
	{
	   _operation_evaluators.resize(255);
	   register_evaluator<account_create_evaluator>()
	   .......
	}   


- class generic_evaluator
- class op_evaluator


.. code-block:: cpp 
	
	void database::initialize_indexes()
	{
	   reset_indexes();
	   _undo_db.set_max_size( GRAPHENE_MIN_UNDO_HISTORY );    

	   .....   
	}  


	void database::init_genesis(const genesis_state_type& genesis_state)
	{ try {
	.....

	}}	
	
   
Evaluators
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- transfer_evaluator 
- override_transfer_evaluator 

-------------------


Return pattern (1)
-------------------

.. code-block:: cpp 

   .......
	signed_transaction trx;
	trx.operations = {op};
	set_operation_fees( trx, _remote_db->get_global_properties().parameters.current_fees);
	trx.validate();

	return sign_transaction(trx, broadcast);


other methods return the same (wallet.cpp)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
	
- transfer_to_blind	
- propose_builder_transaction	
- propose_builder_transaction2
- upgrade_account
- create_asset
- update_asset
- update_asset_issuer
- update_bitasset
- update_asset_feed_producers
- publish_asset_feed
- fund_asset_fee_pool
- claim_asset_fee_pool
- reserve_asset
- global_settle_asset
- settle_asset
- bid_collateral
- whitelist_account
- create_committee_member
- create_witness
- update_witness
- create_worker
- update_worker_votes	
- withdraw_vesting
- vote_for_committee_member
- vote_for_witness
- set_voting_proxy
- set_desired_witness_and_committee_member_count
- sell_asset
- borrow_asset
- borrow_asset_ext
- cancel_order
- transfer
- issue_asset
- propose_parameter_change
- propose_fee_change
- approve_proposal




	
	



|

|

