
.. _wallet-api-calls:

**********************
Wallet API - Calls
**********************

The wallet (``cli_wallet``) requires a running full node to connect to because
it does not offer P2P or blockchain capabilities directly.

If you have not set up your wallet yet, you can find more information on the :ref:`Wallet-CLI Tutorial <wallet-cli-tutorials>` and the :ref:`cli-faq` pages.

.. contents:: Table of Contents
   :local:

-------------
   
General Calls
===========================
help
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::help

gethelp
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::gethelp

info
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::info

about
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::about

network_add_nodes
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::network_add_nodes

network_get_connected_peers
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::network_get_connected_peers


|

**************

Wallet Calls
============================
is_new
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::is_new

is_locked
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::is_locked

lock
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::lock

unlock
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::unlock

set_password
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::set_password

dump_private_keys
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::dump_private_keys

import_key
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::import_key

import_accounts
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::import_accounts

import_account_keys
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::import_account_keys

import_balance
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::import_balance

suggest_brain_key
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::suggest_brain_key

get_transaction_id
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_transaction_id

get_private_key
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_private_key

load_wallet_file
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::load_wallet_file

normalize_brain_key
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::normalize_brain_key

save_wallet_file
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::save_wallet_file


|

**************

Account Calls
====================================
list_my_accounts
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::list_my_accounts

list_accounts
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::list_accounts

list_account_balances
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::list_account_balances

register_account
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::register_account

upgrade_account
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::upgrade_account

create_account_with_brain_key
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::create_account_with_brain_key

transfer
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::transfer

transfer2
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::transfer2

whitelist_account
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::whitelist_account

get_vesting_balances
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_vesting_balances

withdraw_vesting
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::withdraw_vesting

get_account
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_account

get_account_id
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_account_id

get_account_history
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_account_history

approve_proposal
------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::approve_proposal


|

**************

Trading Calls
================================

sell_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::sell_asset

borrow_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::borrow_asset

cancel_order
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::cancel_order

settle_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::settle_asset

get_market_history
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_market_history

get_limit_orders
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_limit_orders

get_call_orders
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_call_orders

get_settle_orders
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_settle_orders

|

**************

Asset Calls
======================================

list_assets
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::list_assets

create_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::create_asset

update_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::update_asset

update_bitasset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::update_bitasset

update_asset_feed_producers
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::update_asset_feed_producers

publish_asset_feed
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::publish_asset_feed

issue_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::issue_asset

get_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_asset

get_bitasset_data
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_bitasset_data

fund_asset_fee_pool
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::fund_asset_fee_pool

reserve_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::reserve_asset

global_settle_asset
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::global_settle_asset


|

**************

Governance
=====================================

create_committee_member
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::create_committee_member

get_witness
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_witness

get_committee_member
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_committee_member

list_witnesses
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::list_witnesses

list_committee_members
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::list_committee_members

create_witness
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::create_witness

update_witness
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::update_witness

create_worker
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::create_worker

update_worker_votes
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::update_worker_votes

vote_for_committee_member
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::vote_for_committee_member

vote_for_witness
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::vote_for_witness

set_voting_proxy
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::set_voting_proxy

set_desired_witness_and_committee_member_count
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::set_desired_witness_and_committee_member_count

propose_parameter_change
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::propose_parameter_change

propose_fee_change
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::propose_fee_change

|

**************

Privacy Mode
================================

set_key_label
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::set_key_label

get_key_label
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_key_label

get_public_key
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_public_key

get_blind_accounts
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_blind_accounts

get_my_blind_accounts
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_my_blind_accounts

get_blind_balances
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_blind_balances

create_blind_account
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::create_blind_account

transfer_to_blind
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::transfer_to_blind

transfer_from_blind
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::transfer_from_blind

blind_transfer
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::blind_transfer

blind_history
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::blind_history

receive_blind_transfer
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::receive_blind_transfer


|

**************


Blockchain Inspection
=====================================

get_block
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_block

get_account_count
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_account_count

get_global_properties
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_global_properties

get_dynamic_global_properties
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_dynamic_global_properties

get_object
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_object


|

**************

.. _w-api-trans-builder:


Transaction Builder
==================================

begin_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::begin_builder_transaction

add_operation_to_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::add_operation_to_builder_transaction

replace_operation_in_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::replace_operation_in_builder_transaction

set_fees_on_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::set_fees_on_builder_transaction

preview_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::preview_builder_transaction

sign_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::sign_builder_transaction

propose_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::propose_builder_transaction

propose_builder_transaction2
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::propose_builder_transaction2

remove_builder_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::remove_builder_transaction

serialize_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::serialize_transaction

sign_transaction
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::sign_transaction

get_prototype_operation
--------------------------------------
.. doxygenfunction:: graphene::wallet::wallet_api::get_prototype_operation


|


