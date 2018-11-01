
.. _reg-acc-smartcontract:

Register Account - smart contract
***********************

In this section, we examine how worker proposal  smart contract was constructed. There are items and helpful steps to construct a smart contract. We want to list those items by examining BitShares blockchain components.
* **Smart contract items**

  - Object
  - Object Index and Call
  - Operations
  - Validations
  - Initialize Evaluators and Index
  - Evaluators
  - API call

-------------------

Items
========================

Object
---------------------------------------------
- account_object



Object Index and Search Call
---------------------------------------------
- account_member_index
- account_referrer_index

- get_account( registrar_account )


Operations
---------------------------------------------
- account_create_operation


- signed_transaction tx;
  - struct signed_transaction : public transaction

Validations
---------------------------------------------
- tx.validate();

.. code-block:: cpp 

	processed_transaction database::validate_transaction( const signed_transaction& trx )
	{
	   auto session = _undo_db.start_undo_session();
	   return _apply_transaction( trx );
	}


Initialize Evaluators and Index
---------------------------------------------


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
---------------------------------------------

.. code-block:: cpp 

	class account_create_evaluator : public evaluator<account_create_evaluator>
	{
	public:
	   typedef account_create_operation operation_type;

	   void_result do_evaluate( const account_create_operation& o );
	   object_id_type do_apply( const account_create_operation& o ) ;
	};

CLI Wallet Calls
---------------------------------------------
- 

API calls
---------------------------------------------

.. code-block:: cpp 


  signed_transaction register_account(string name,
                                       public_key_type owner,
                                       public_key_type active,
                                       string  registrar_account,
                                       string  referrer_account,
                                       uint32_t referrer_percent,
                                       bool broadcast = false)
									   
									   
--------------------


broadcast transaction 
^^^^^^^^^^^^^^^^^^^^^^^

Before the end a method, as one of patterns, 

  - ``signed_transaction`` instance is created, 
  - (do some processes...)
  - check `broadcase1 flag 
  - if true, send the instance bt ``broadcast_transaction``
  
and return the transaction instance
  

.. code-block:: cpp 

   signed_transaction tx;
   // do some processes...
   ..........
   if( broadcast )
 	 _remote_net_broadcast->broadcast_transaction( tx );

   return tx;
  
  

other methods that have the same pattern ``return`` (wallet.cpp)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	
- register_account	  
- create_account_with_private_key  
- sign_transaction
- blind_transfer_help	  
	  

											   

|

