


Database API
*******************

The database API is available from the full node via websockets.

If you have not set up your websockets connection, please read :doc:`this
article <websocket>`.


.. contents:: Table of Contents
   :local:
   
----------
   

Objects
=================

get_objects
-------------------------
.. doxygenfunction:: graphene::app::database_api::get_objects

|

**************

Subscriptions
================
set_subscribe_callback
-------------------------
.. doxygenfunction:: graphene::app::database_api::set_subscribe_callback

set_pending_transaction_callback
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::set_pending_transaction_callback

set_block_applied_callback
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::set_block_applied_callback

cancel_all_subscriptions
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::cancel_all_subscriptions

|

**************

Blocks and transactions
=========================

get_block_header
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_block_header

get_block
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_block

get_transaction
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_transaction

get_recent_transaction_by_id
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_recent_transaction_by_id

|

**************

Globals
===============
get_chain_properties
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_chain_properties

get_global_properties
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_global_properties

get_config
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_config

get_chain_id
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_chain_id

get_dynamic_global_properties
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_dynamic_global_properties

|

**************

Keys
===========
get_key_references
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_key_references

|

**************

Accounts
=============

get_accounts
---------------------
.. doxygenfunction:: graphene::app::database_api::get_accounts

get_full_accounts
---------------------
.. doxygenfunction:: graphene::app::database_api::get_full_accounts

get_account_by_name
----------------------
.. doxygenfunction:: graphene::app::database_api::get_account_by_name

get_account_references
------------------------
.. doxygenfunction:: graphene::app::database_api::get_account_references

lookup_account_names
--------------------------
.. doxygenfunction:: graphene::app::database_api::lookup_account_names

lookup_accounts
-------------------------
.. doxygenfunction:: graphene::app::database_api::lookup_accounts

get_account_count
-------------------------
.. doxygenfunction:: graphene::app::database_api::get_account_count


|

**************

Balances
===========
get_account_balances
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_account_balances

get_named_account_balances
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_named_account_balances

get_balance_objects
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_balance_objects

get_vested_balances
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_vested_balances

get_vesting_balances
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_vesting_balances

|

**************

Assets
===========
get_assets
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_assets

list_assets
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::list_assets

lookup_asset_symbols
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::lookup_asset_symbols

|

**************

Markets / feeds
=================
get_order_book
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_order_book

get_limit_orders
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_limit_orders

get_call_orders
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_call_orders

get_settle_orders
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_settle_orders

get_margin_positions
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_margin_positions

subscribe_to_market
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::subscribe_to_market

unsubscribe_from_market
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::unsubscribe_from_market

get_ticker
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_ticker

get_24_volume
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_24_volume

get_trade_history
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_trade_history

|

**************

Witnesses
===============
get_witnesses
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_witnesses

get_witness_by_account
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_witness_by_account

lookup_witness_accounts
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::lookup_witness_accounts

get_witness_count
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_witness_count

|

**************

Committee members
====================
get_committee_members
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_committee_members

get_committee_member_by_account
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_committee_member_by_account

lookup_committee_member_accounts
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::lookup_committee_member_accounts


|

**************

Workers
=================
get_workers_by_account
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_workers_by_account

|

**************

Votes
=============
lookup_vote_ids
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::lookup_vote_ids

|

**************

Authority / Validation
=======================
get_transaction_hex
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_transaction_hex

get_required_signatures
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_required_signatures

get_potential_signatures
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_potential_signatures

get_potential_address_signatures
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_potential_address_signatures

verify_authority
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::verify_authority

verify_account_authority
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::verify_account_authority

validate_transaction
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::validate_transaction

get_required_fees
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_required_fees

|

**************

Proposed Transactions
======================

get_proposed_transactions
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_proposed_transactions

|

**************

Blinded balances
====================

get_blinded_balances
-------------------------------------
.. doxygenfunction:: graphene::app::database_api::get_blinded_balances



|
