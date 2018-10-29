
.. _trx-smartcontract:

Transaction - smart contract
***********************

Transaction
========================


Research 
----------------

Define your Object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- account_object


Create Object Index and Search Call
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
-  class transaction_object : public abstract_object<transaction_object>


Define Operations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- account_create_operation


- signed_transaction tx;
  - struct signed_transaction : public transaction

Create Validations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- tx.validate();

.. code-block:: cpp 



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





Higher Level DAPP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

	
	



|

|

