
.. _worker-smartcontract:

Worker Proposal - smart contract
***********************

"A worker proposal is a smart contract consisting of several operations and make use of the maintenance interval to automatically do stuff."

In this section, we examine how worker proposal  smart contract was constructed. There are items and helpful steps to construct a smart contract. We want to list those items by examining BitShares blockchain components.

* **Smart contract items**

  - Object
  - Object Index and Call
  - Operations
  - Validations
  - Initialize Evaluators and Index
  - Evaluators
  - API call

-------

Items
========================

Object
---------------------------------------------

- worker_object



Object Index and Search Call
---------------------------------------------
- 

Operations
---------------------------------------------
- 

Validations
---------------------------------------------

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


- generic_evaluator
- op_evaluator



CLI Wallet Calls
---------------------------------------------
- 

API calls
---------------------------------------------
-


------------------------




