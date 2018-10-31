.. _ex-broadcase_trnx:

Features: Broadcast and Transactions 
******************************************


Clients and BitShares blockchain network
========================================

In this section, we look into BitShares Blockchain transaction processes and the flow. The below image is simplified clients and nodes relations and shows two transactions occurred and nodes received the transactions. 

.. image:: ../../_static/output/net-broadcast2.png
        :alt: BitShares 
        :width: 700px
        :align: center


On the BitShares blockchain, every node receives a transaction (1a&1b), locally validates its content and signature(2a&2b), and re-broadcasts to its connected peers(3a&3b). The connected peers could be another API node or witness node. 

Block producer nodes (a.k.a. witness nodes) will also sign blocks if it is their turn (time slot)(4b), produce a block(5b), and broadcast(6b) that block to its connected peers.

If other nodes receive that block, they attempt it to make their copy of the blockchain.

Block producing nodes provide the blockchain with signed (like to think about it as "certified") blocks containing all new data that is previously submitted by account holders through API nodes.

Each block contains more than one transactions. Each transaction contains more than one operations and each operation has a "fee" element and other elements.  You can find more :ref:`information about block here <lib-block>`.

Witnesses are selected based upon their commitment to remain neutral to blockchain policy.  Image, for extremely important contracts, a public notify is sometimes used. Neither witnesses nor notaries are party to the contract, but they serve a very important role of certifying the the contract was signed by the specified individuals at the specifies time. 

In BitShares witnesses serve a similar role of validating signatures and time-stamping transactions by including them in blocks. 

|

--------------



Broadcast transaction processes
==============================

We look into Wallet plugin to learn about `broadcast transaction`; the features and definitions.

Newwork_broadcast_api has ``broadcase_transaction`` and ``broadcast_transaction_with_callback`` methods to handle broadcast transaction. 

:broadcase_transaction:  Broadcast a transaction to the network. The transaction will be checked for validity in the local database prior to broadcasting. If it fails to apply locally, an error will be thrown and the transaction will not be broadcast. 

:broadcast_transaction_with_callback:  This version of broadcast transaction registers a callback method that will be called when the transaction is included into a block. The callback method includes the transaction id, block number, and transaction number in the block. 

|

Example Steps - broadcast transaction 
-------------------------------

Pattern (1)
^^^^^^^^^^^^^^^^^^^^^

Before the end a method; 

  1. Create ``signed_transaction`` instance
  2. Set an operation
  3. Set ``set_operation_fee``
  4. validate the instance

and return ``sign_transaction`` with the instance and broadcast flag.  
  

.. code-block:: cpp 

   .......
	signed_transaction trx;
	trx.operations = {op};
	set_operation_fees( trx, _remote_db->get_global_properties().parameters.current_fees);
	trx.validate();

	return sign_transaction(trx, broadcast);

	
other methods that have the same pattern (wallet.cpp)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
	
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


	
Pattern (2)
^^^^^^^^^^^^^^^^^^^^^

Before the end a method,

  1. ``signed_transaction`` instance is created, 
  2. (do some processes...)
  3. check `broadcase1 flag 
  4. if true, send the instance bt ``broadcast_transaction``
  
and return the transaction instance
  

.. code-block:: cpp 

   signed_transaction tx;
   // do some processes...
   ..........
   if( broadcast )
 	 _remote_net_broadcast->broadcast_transaction( tx );

   return tx;
  
  

other methods that have the same pattern (wallet.cpp)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	
- register_account	  
- create_account_with_private_key  
- sign_transaction
- blind_transfer_help	  
	  

----

samples

.. code-block:: cpp 

    void network_broadcast_api::broadcast_transaction(const signed_transaction& trx)
    {
       trx.validate();
       _app.chain_database()->push_transaction(trx);
       if( _app.p2p_node() != nullptr )
          _app.p2p_node()->broadcast_transaction(trx);
    }

	
	
.. code-block:: cpp 
	
    void network_broadcast_api::broadcast_transaction_with_callback(confirmation_callback cb, const signed_transaction& trx)
    {
       trx.validate();
       _callbacks[trx.id()] = cb;
       _app.chain_database()->push_transaction(trx);
       if( _app.p2p_node() != nullptr )
          _app.p2p_node()->broadcast_transaction(trx);
    }
	  
	  
|

|

