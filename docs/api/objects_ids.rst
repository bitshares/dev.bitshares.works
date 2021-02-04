
.. _objects-ids:

Objects and IDs
==================


.. contents:: Table of Contents
   :local:
   
-------

BitShares has a special
model to represent the blockchain, its transactions and accounts. This chapter
wants to given an introduction to the concepts of *objects* as they are used by
the BitShares clients. Furthermore, we will briefly introduce the API and
show how to subscribe to object changes (such as new blocks or incoming
deposits). Afterwards, we will show how exchange may monitor their accounts and
credit incoming funds to their corresponding users.

Objects
--------------

On the BitShares blockchain there are no addresses, but objects identified by a
unique *id*, an *type* and a *space* in the form:::

    space.type.id

List of commonly used objects
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------+------------------------------------+ 
| ID     | Object Type                        |
+========+====================================+ 
| 1.2.x  | account object                     | 
+--------+------------------------------------+ 
| 1.3.x  | asset object                       | 
+--------+------------------------------------+ 
| 1.4.x  | force settlement object            | 
+--------+------------------------------------+ 
| 1.5.x  | committee member object            | 
+--------+------------------------------------+ 
| 1.6.x  | witness object                     | 
+--------+------------------------------------+ 
| 1.7.x  | limit order object                 | 
+--------+------------------------------------+ 
| 1.8.x  | call order object                  | 
+--------+------------------------------------+ 
| 1.10.x | proposal object                    | 
+--------+------------------------------------+ 
| 1.11.x | operation history object           | 
+--------+------------------------------------+ 
| 1.12.x | withdraw permission object         | 
+--------+------------------------------------+ 
| 1.13.x | vesting balance object             | 
+--------+------------------------------------+ 
| 1.14.x | worker object                      | 
+--------+------------------------------------+ 
| 1.15.x | balance object                     | 
+--------+------------------------------------+ 
| 1.16.x | htlc object                        | 
+--------+------------------------------------+ 
| 1.18.x | ticket object                      | 
+--------+------------------------------------+ 
| 1.19.x | liquidity pool object              | 
+--------+------------------------------------+ 
| 2.0.x  | global_property_object             | 
+--------+------------------------------------+ 
| 2.1.x  | dynamic_global_property_object     | 
+--------+------------------------------------+ 
| 2.3.x  | asset_dynamic_data                 | 
+--------+------------------------------------+ 
| 2.4.x  | asset_bitasset_data                | 
+--------+------------------------------------+ 
| 2.5.x  | account_balance_object             | 
+--------+------------------------------------+ 
| 2.6.x  | account_statistics_object          | 
+--------+------------------------------------+ 
| 2.7.x  | transaction_history_object         | 
+--------+------------------------------------+ 
| 2.8.x  | block_summary_object               | 
+--------+------------------------------------+ 
| 2.9.x  | account_transaction_history_object | 
+--------+------------------------------------+ 
| 2.10.x | blinded_balance_object             | 
+--------+------------------------------------+ 
| 2.11.x | chain_property_object              | 
+--------+------------------------------------+ 
| 2.12.x | witness_schedule_object            | 
+--------+------------------------------------+ 
| 2.13.x | budget_record_object               | 
+--------+------------------------------------+ 
| 2.14.x | special_authority_object           | 
+--------+------------------------------------+ 
| 2.15.x | buyback_object                     | 
+--------+------------------------------------+ 
| 2.16.x | fba_accumulator_object             | 
+--------+------------------------------------+ 
| 2.17.x | collateral_bid_object              | 
+--------+------------------------------------+ 

- The full list can be found in the sources `here <https://github.com/bitshares/bitshares-core/blob/master/libraries/protocol/include/graphene/protocol/types.hpp>`_ and `here <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/types.hpp>`_.

Examples
^^^^^^^^^^^^^

To get a feeling about what these objects do, we recommend to obtain these
exemplary objects.

 * ``2.0.0`` (global blockchain parameters)
 * ``2.1.0`` (current blockchain data)
 * ``1.2.0`` (committee-account details)
 * ``1.3.0`` (core asset details)

 
+-------------+------------------------------------------------------+ 
| Object ID   | Translates to                                        |
+=============+======================================================+ 
| 1.11.\<id\> | 	operation with id <id>                       |   
+-------------+------------------------------------------------------+ 
| 1.14.7      | # protocol space / worker / id: 7                    | 
+-------------+------------------------------------------------------+ 
| 1.2.15      | # protocol space / account / id: 15                  |
+-------------+------------------------------------------------------+ 
| 1.6.\<id\>  | 	block signer <id>                            |
+-------------+------------------------------------------------------+ 
| 1.6.105     |  # protocol space / witness / id: 105                |   
+-------------+------------------------------------------------------+ 
| 2.1.0       |  # implementation space / dynamic global properties  |
+-------------+------------------------------------------------------+ 
| 2.3.8       |  # implementation space / asset . id: 8              |
+-------------+------------------------------------------------------+ 


Examples - Accounts
^^^^^^^^^^^^^^^^^^^

The BitShares blockchain users are requires to register each account with a unique username and a public key on the blockchain. The blockchain assigns an incremental user id and offers to resolve the name-to-id pair. 


+-------------+---------------------------------------------------------+ 
| Object ID   | Translates to                                           |
+=============+=========================================================+ 
| 1.2.80      | # protocol space / account / id: 80                     |
+-------------+---------------------------------------------------------+ 
| 2.5.80      | # implementation space / account-balance / id: 80       |
+-------------+---------------------------------------------------------+ 
| 2.6.80      | # implementation space / account-statistics / id: 80    |
+-------------+---------------------------------------------------------+ 
| 2.9.80      | # implementation space / account-transactions / id: 80  |
+-------------+---------------------------------------------------------+ 

   
- A programmatic description of all fields can be found in the `sources <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/account_object.hpp>`_.

**Example Call and a result: account object (id: 1.2.x)**::

		unlocked >>> get_account_id "user123"
		"1.2.539269"
		unlocked >>>

Examples - Assets
^^^^^^^^^^^^^^^^^^^
   

**Example Call and a result: asset object (id: 1.3.x)**::

		unlocked >>> list_assets "BTS" "2"
		[{
				"id": "1.3.0",
				"symbol": "BTS",
				"precision": 5,
				"issuer": "1.2.3",

			....
				"dynamic_asset_data_id": "2.3.368"
			}
		]

