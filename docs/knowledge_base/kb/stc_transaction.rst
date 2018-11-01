
.. _trx-smartcontract:

Transaction - smart contract
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


Create Object Index and Search Call
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
-  class transaction_object : public abstract_object<transaction_object>


Object Index and Search Call
---------------------------------------------
- account_create_operation


- signed_transaction tx;
  - struct signed_transaction : public transaction

Operations
---------------------------------------------

  
Validations
---------------------------------------------
- tx.validate();

.. code-block:: cpp 



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


Prepare Test Cases
---------------------------------------------


CLI Wallet Calls
---------------------------------------------


API calls
---------------------------------------------

.. code-block:: cpp 





|

|

