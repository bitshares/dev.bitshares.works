
.. _reg-acc-smartcontract:

Register Account - smart contract
***********************

Register account
========================


Research 
----------------

Define your Object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- account_object


Create Object Index and Search Call
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- account_member_index
- account_referrer_index

- get_account( registrar_account )


Define Operations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- account_create_operation


- signed_transaction tx;
  - struct signed_transaction : public transaction

Create Validations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- tx.validate();

.. code-block:: cpp 

	processed_transaction database::validate_transaction( const signed_transaction& trx )
	{
	   auto session = _undo_db.start_undo_session();
	   return _apply_transaction( trx );
	}


Create Initialize Evaluators and Index
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
	
	
	
   
Create Evaluators
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp 

	class account_create_evaluator : public evaluator<account_create_evaluator>
	{
	public:
	   typedef account_create_operation operation_type;

	   void_result do_evaluate( const account_create_operation& o );
	   object_id_type do_apply( const account_create_operation& o ) ;
	};


Return pattern (2)
-------------------

.. code-block:: cpp 

      if( broadcast )
         _remote_net_broadcast->broadcast_transaction( tx );
		// do something... 
		 		 
      return tx;
	  
	  
	  

other methods return the same (wallet.cpp)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

	  
create_account_with_private_key  
sign_transaction
blind_transfer_help	  
	  

											   
	  
Create Automatic Actions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Create Hardfork Guards
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- clean up


Building
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Prepare Test Cases
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


CLI Wallet Calls
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


API calls
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. code-block:: cpp 


  signed_transaction register_account(string name,
                                       public_key_type owner,
                                       public_key_type active,
                                       string  registrar_account,
                                       string  referrer_account,
                                       uint32_t referrer_percent,
                                       bool broadcast = false)


Higher Level DAPP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

	
	



|

|

