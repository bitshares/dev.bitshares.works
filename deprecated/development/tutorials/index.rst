
.. _dev-guides:

Developer Guide (Tutorials)
*****************************

General
==================

Address structure, Format, block, time, and other
---------------------------------------------------  

.. _dev-faq17:
  
What is the standard Bitshares address structure and format?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

address = 'BTS'+base58(ripemd(sha512(compressed_pub)))  (checksum obviated)
But addresses are not used directly, instead you have an account (that can be controlled by one or more address, pubkey or another account).
https://bitshares.org/technology/dynamic-account-permissions/

.. _dev-faq15:

What is the format of the block header?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: cpp

    struct block_header
       {
          digest_type                   digest()const;
          block_id_type                 previous;
          uint32_t                      block_num()const { return num_from_id(previous) + 1; }
          fc::time_point_sec            timestamp;
          witness_id_type               witness;
          checksum_type                 transaction_merkle_root;
          extensions_type               extensions;

          static uint32_t num_from_id(const block_id_type& id);
       };

.. _dev-faq16:
	   
What is the maximum bitshares block size?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Configurable by chain parameters.

.. code-block:: cpp

   struct chain_parameters
   {
      /** using a smart ref breaks the circular dependency created between operations and the fee schedule */
      smart_ref<fee_schedule> current_fees;                       ///< current schedule of fees
      uint8_t                 block_interval                      = GRAPHENE_DEFAULT_BLOCK_INTERVAL; ///< interval in seconds between blocks
      uint32_t                maintenance_interval                = GRAPHENE_DEFAULT_MAINTENANCE_INTERVAL; ///< interval in sections between blockchain maintenance events
      uint8_t                 maintenance_skip_slots              = GRAPHENE_DEFAULT_MAINTENANCE_SKIP_SLOTS; ///< number of block_intervals to skip at maintenance time
      uint32_t                committee_proposal_review_period    = GRAPHENE_DEFAULT_COMMITTEE_PROPOSAL_REVIEW_PERIOD_SEC; ///< minimum time in seconds that a proposed transaction requiring committee authority may not be signed, prior to expiration
      uint32_t                maximum_transaction_size            = GRAPHENE_DEFAULT_MAX_TRANSACTION_SIZE; ///< maximum allowable size in bytes for a transaction
      uint32_t                maximum_block_size                  = GRAPHENE_DEFAULT_MAX_BLOCK_SIZE; ///< maximum allowable size in bytes for a block
      uint32_t                maximum_time_until_expiration       = GRAPHENE_DEFAULT_MAX_TIME_UNTIL_EXPIRATION; ///< maximum lifetime in seconds for transactions to be valid, before expiring
      uint32_t                maximum_proposal_lifetime           = GRAPHENE_DEFAULT_MAX_PROPOSAL_LIFETIME_SEC; ///< maximum lifetime in seconds for proposed transactions to be kept, before expiring
      uint8_t                 maximum_asset_whitelist_authorities = GRAPHENE_DEFAULT_MAX_ASSET_WHITELIST_AUTHORITIES; ///< maximum number of accounts which an asset may list as authorities for its whitelist OR blacklist
      uint8_t                 maximum_asset_feed_publishers       = GRAPHENE_DEFAULT_MAX_ASSET_FEED_PUBLISHERS; ///< the maximum number of feed publishers for a given asset
      uint16_t                maximum_witness_count               = GRAPHENE_DEFAULT_MAX_WITNESSES; ///< maximum number of active witnesses
      uint16_t                maximum_committee_count             = GRAPHENE_DEFAULT_MAX_COMMITTEE; ///< maximum number of active committee_members
      uint16_t                maximum_authority_membership        = GRAPHENE_DEFAULT_MAX_AUTHORITY_MEMBERSHIP; ///< largest number of keys/accounts an authority can have
      uint16_t                reserve_percent_of_fee              = GRAPHENE_DEFAULT_BURN_PERCENT_OF_FEE; ///< the percentage of the network's allocation of a fee that is taken out of circulation
      uint16_t                network_percent_of_fee              = GRAPHENE_DEFAULT_NETWORK_PERCENT_OF_FEE; ///< percent of transaction fees paid to network
      uint16_t                lifetime_referrer_percent_of_fee    = GRAPHENE_DEFAULT_LIFETIME_REFERRER_PERCENT_OF_FEE; ///< percent of transaction fees paid to network
      uint32_t                cashback_vesting_period_seconds     = GRAPHENE_DEFAULT_CASHBACK_VESTING_PERIOD_SEC; ///< time after cashback rewards are accrued before they become liquid
      share_type              cashback_vesting_threshold          = GRAPHENE_DEFAULT_CASHBACK_VESTING_THRESHOLD; ///< the maximum cashback that can be received without vesting
      bool                    count_non_member_votes              = true; ///< set to false to restrict voting privlegages to member accounts
      bool                    allow_non_member_whitelists         = false; ///< true if non-member accounts may set whitelists and blacklists; false otherwise
      share_type              witness_pay_per_block               = GRAPHENE_DEFAULT_WITNESS_PAY_PER_BLOCK; ///< CORE to be allocated to witnesses (per block)
      uint32_t                witness_pay_vesting_seconds         = GRAPHENE_DEFAULT_WITNESS_PAY_VESTING_SECONDS; ///< vesting_seconds parameter for witness VBO's
      share_type              worker_budget_per_day               = GRAPHENE_DEFAULT_WORKER_BUDGET_PER_DAY; ///< CORE to be allocated to workers (per day)
      uint16_t                max_predicate_opcode                = GRAPHENE_DEFAULT_MAX_ASSERT_OPCODE; ///< predicate_opcode must be less than this number
      share_type              fee_liquidation_threshold           = GRAPHENE_DEFAULT_FEE_LIQUIDATION_THRESHOLD; ///< value in CORE at which accumulated fees in blockchain-issued market assets should be liquidated
      uint16_t                accounts_per_fee_scale              = GRAPHENE_DEFAULT_ACCOUNTS_PER_FEE_SCALE; ///< number of accounts between fee scalings
      uint8_t                 account_fee_scale_bitshifts         = GRAPHENE_DEFAULT_ACCOUNT_FEE_SCALE_BITSHIFTS; ///< number of times to left bitshift account registration fee at each scaling
      uint8_t                 max_authority_depth                 = GRAPHENE_MAX_SIG_CHECK_DEPTH;
      extensions_type         extensions;

      /** defined in fee_schedule.cpp */
      void validate()const;
   };

.. _dev-faq1:

Are there any sharding mechanics currently deployed?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
No

.. _dev-faq4:

How are SPV clients handled?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
No SPV clients at the moment, each full node can expose a public websocket/http api.

.. _dev-faq8:

How is time addressed in the blockchain? Is NTP used or some other protocol?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
NTP

.. _dev-faq6:

How do new clients bootstrap into the network?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Trusted seed nodes. Knowledge of initial witness keys.

.. _dev-faq13:	   
	   
What is the average block time?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Current 3 seconds, configurable by chain parameters.

.. _dev-faq7:

How is accounting addressed in Bitshares? Is it a Nxt style accounting model or like Bitcoin's UTXO
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each account has a finite set of balances, one for each asset type.


Protocol
--------------

.. _dev-faq2:

Are there any special affordances made for privacy?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
...such as using CoinJoin or a ZK-SNARK based privacy scheme like Zerocash? If
mixing is integrated at the protocl level are you using the standards set forth
by the BNMCKF Mixcoin proposal

Confidential values (same as blockstream elements using the same secp256k1-zkp lib) + stealth addresses.
https://github.com/ElementsProject/elementsproject.github.io/blob/master/confidential_values.md
No mixing, No CoinJoin.

.. _dev-faq3:

Does the protocol provide mechanisms for overlay protocols to interact such as OR_RETURN?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Yes, using a custom_operation.

.. code-block:: cpp

    struct custom_operation : public base_operation
       {
          struct fee_parameters_type {
             uint64_t fee = GRAPHENE_BLOCKCHAIN_PRECISION;
             uint32_t price_per_kbyte = 10;
          };

          asset                     fee;
          account_id_type           payer;
          flat_set<account_id_type> required_auths;
          uint16_t                  id = 0;
          vector<char>              data;

          account_id_type   fee_payer()const { return payer; }
          void              validate()const;
          share_type        calculate_fee(const fee_parameters_type& k)const;
       };

.. _dev-faq11:

Is this done via a gossip protocol or through a federate relay?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each node immediately broadcast the data it receives to its peers after validating it
https://github.com/cryptonomex/graphene/blob/master/libraries/p2p/design.md


Data Structures
----------------------

.. _dev-faq12:

What data structures are used in the blockchain?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::
    Blocks => transactions => operations => objects.

The blockchain state is contained in an object database that is affected by the operations.
Example objects:::

    account_object
    asset_object
    account_balance_object
    ...

.. code-block:: cpp

    class account_balance_object : public abstract_object<account_balance_object>
       {
          public:
             static const uint8_t space_id = implementation_ids;
             static const uint8_t type_id  = impl_account_balance_object_type;

             account_id_type   owner;
             asset_id_type     asset_type;
             share_type        balance;

             asset get_balance()const { return asset(balance, asset_type); }
             void  adjust_balance(const asset& delta);
       };

Public key system
------------------------


.. _dev-faq18:

What public key system is used? If elliptic curve, then what is the curve?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Same as Bitcoin, secp256k1.


Scripting Language ?
--------------------

.. _dev-faq10:
	   
Is there a specification for Bitshares scripting language? (assuming there is one)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
No scripting

.. _dev-faq9:

Is the scripting language turing complete?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
No scripting

	   
------------------

.. _distributed-access-to-dex:

BitShares Decentralised Exchange (DEX) 
=======================================

.. include:: distributed-access-hosting.rst


.. _create-account-dev:

Account
=============

.. include:: account-create.rst

Vesting Balance
=================

.. _list-vesting-balances:

.. include:: vesting-list.rst

------------

.. _claiming-vesting-balance:

.. include:: vesting-claim.rst

-------------

.. _assets-guide:
   
Assets
============


.. _creating-new-uia1:

.. include:: uia-create-gui.rst

-----------

.. _uia-create-manual:

.. include:: uia-create-manual.rst

-----------

.. _mpa-create-manual:

.. include:: mpa-create-manual.rst 

-----------

.. _uia-update-manual:


.. include:: uia-update-manual.rst

-------------

.. _publish-feed:

.. include:: publish-feed.rst

-------------
.. _asset-faq:


Fee Pool
------------

.. _asset-faq5:

What is Fee Pool Draining?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If an order is created and paid in a non-BTS asset, the fee is
implicitly exchange into BTS to pay the network fee. However, if the
order is canceled, 90% of the fee will be returned as BTS. The result
is, that if the core exchange rate is lower than the highest bid, people
can simply buy your token from the market, and exchange them implicitly
with the fee pool by creating and canceling an order. This will deplete
the fee pool and leave the issuer with his tokens at a slight loss
(depending on the offset of the core exchange rate). For this reason, we
recommend to use a core exchange that is slightly higher than the market
price of your asset. As a consequence, paying fees in BTS should always
be cheaper.

.. _asset-faq6:

What is the fee pool all about?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The fee pool allows participants in the network to deal with assets and
pay for the transaction fees without the need to hold BTS. Any
transaction fee can be paid by paying any asset that has a core exchange
rate (i.e. a price) at which the asset can be exchange implicitly into
BTS to cover the network fee. If the asset's fee pool is funded, the
fees can be payed in the native UIA instead of BTS.

.. note:: The core exchange rate at which a fee can be exchanged into
          BTS may differ from the actual market valuation of the asset.
          A user, thus, may pay a premium or spare funds by paying in
          BTS.

.. warning:: Make sure your core exchange rate is higher than the lowest
             ask, otherwise, people will buy your token from the market
             and drain your fee pool via implicit abitrage.

It is the task of the issuer to keep the fee pool funded and the core
exchange rate updated unless he wants the owner of his asset to be
required to hold BTS for the fee.

.. _asset-faq7:

What to do if the fee pool is empty?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Open up the issuer's account, click the assets tab and open up the
dialog to change the asset. There will be a fee pool tab that allows you
to fund the fee pool and claim the accumulated fees!


---------

Market Fees
---------------

.. _asset-faq8:

How to claim accumulated fees?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Open up the issuer's account, click the assets tab and open up the
dialog to change the asset. There will be a fee pool tab that allows you
to fund the fee pool and claim the accumulated fees!

.. _asset-faq9:

What are Asset Flags and Permissions?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When an asset is creatd, the issuer can set any combination of
flags/permissions. **Flags** are set in stone unless there is
**permission** to edit. Once a permission to edit is revoked, flags are
permanent, and can never be modified again.

.. _asset-faq10:

What are the Flags?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* ``charge_market_fee``:
  an issuer-specified percentage of all market trades in this asset is
  paid to the issuer
* ``white_list``:
  accounts must be white-listed in order to hold this asset
* ``override_authority``:
  issuer may transfer asset back to himself
* ``transfer_restricted``:
  require the issuer to be one party to every transfer
* ``disable_force_settle``:
  disable force settling
* ``global_settle``: (only for bitassets)
  allows bitasset issuer to force a global settling - this may be set
  in permissions, but should not be set as flag unless, for instance, a
  prediction market has to be resolved. If this flag has been enabled,
  no further shares can be borrowed!
* ``disable_confidential``:
  allow the asset to be used with confidential transactions
* ``witness_fed_asset``:
  allow the asset to be fed by witnesses
* ``committee_fed_asset``:
  allow the asset to be fed by the committee

 .. _asset-faq11:
 
What are the Permissions?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Enable market fee
* Require holders to be white-listed
* Issuer may transfer asset back to himself
* Issuer must approve all transfers
* Disable confidential transactions

.. _asset-faq12:

What happens if I enable Market fees?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If *Market Fees* of a UIA are turned on, fees have to be payed for each
**market transaction**. This means, that market fees only apply to
**filled orders**!

The percentage of market fees that are applied can be defined and
changed by the issuer and any fee generated that way will be accumulated
for each asset only to be claimed by the issuer.

If the Market Fee is set to 1%, the issuer will earn 1% of market volume
as profit. These profits are accumulated for each UIA and can be
withdrawn by the issuer.

.. _asset-faq13:

What if two different market fees are involved in a trade?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Suppose, I set the market fee for MyUIA market at 0.1%.
and the market fee for YourUIA market at 0.3%.

In BitShares, You pay the fee upon **receiving an asset**. Hence, one
side will pay 0.3% the other will pay 0.1%.




---------  
   
  
Market Pegged Assets
------------------------

.. _asset-faq14:

Can I use the same flags/permissions as for UIAs?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Yes!

.. _asset-faq15:

What are market-pegged-asset-specific parameters?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* ``feed_lifetime_sec``:
  The lifetime of a feed. After this time (in seconds) a feed is no
  longer considered *valid*.
* ``minimum_feeds``:
  The number of feeds required for a market to become (and stay) active.
* ``force_settlement_delay_sec``:
  The delay between requesting a settlement and actual execution of
  settlement (in seconds)
* ``force_settlement_offset_percent``:
  A percentage offset from the price feed for settlement (`100% = 10000`)
* ``maximum_force_settlement_volume``:
  Maximum percentage of the supply that can be settled per day (`100% = 10000`)
* ``short_backing_asset``:
  The asset that has to be used to *back* this asset (when borrowing)

  
---------------------

Questions - Assets
-----------------------------

.. _asset-faq1:

Can I change `x` after creation of the asset
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following parameters can be changed after creation:

* Issuer
* UIA-Options:
	* Max Supply
	* Market Fee
	* Permissions (disable only/nor re-enable)
	* Flags (if permissions allow it)
	* Core exchange rate
	* White/Black Listing
	* Description

* MPG-Options:
	* Feed Life Time
	* Minimum Feeds
	* Force Settlement Offset/Delay/Volume

Things that cannot be changes:

* Symbol
* Precision

A guide can be found :ref:`here <uia-update-manual>`.

.. _asset-faq2:

Changing the issuer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The current issue of an asset may transfer ownership of the asset to
someone else by changing the issuer in the asset's settings.

.. _asset-faq3:

What about Parent and Child assets?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A **parent**/**child** relation ship for assets can be represented by
the name of the symbol, e.g.::

    PARENT.child

can only be created by the issuer of ``PARENT`` and no one else.

.. _asset-faq4:

What happens to the asset creation fee?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

50% of the asset creation fee are used to pre-fill the assets fee pool.
From the other 50%, 20% go to the network and 80% go to the referral
program. This means, that if you are a life-time member, you get back
40% of the asset creation fee after the vesting period (currently 90
days).


 

-------------

.. _pm-guide:
   
Prediction Markets
===================

.. _pm-create-manual:

.. include:: pm-create-manual.rst

------------

.. _pm-close-manual:

.. include:: pm-close-manual.rst

------------

 
Transfer / Transactions
==========================

Questions - Transactions
-----------------------------------

.. _dev-faq5:

How are transactions validated?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each operation has a defined evaluator that checks for preconditions
(do_evaluate) and modify the state (do_apply). (After signature verification)

.. code-block:: cpp

    class transfer_evaluator : public evaluator<transfer_evaluator>
       {
          public:
             typedef transfer_operation operation_type;

             void_result do_evaluate( const transfer_operation& o );
             void_result do_apply( const transfer_operation& o );
       }

  
.. _dev-faq14:
	   
What is the average size in Bytes of a Bitshares transaction?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Average wire size of operations is ~30 bytes.
Average mem size of operations is ~100 bytes.
https://github.com/cryptonomex/graphene/blob/master/programs/size_checker/main.cpp


.. _dev-faq19:

What transaction types are natively supported?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Transaction are composed of operations (about ~40 different types).
Example of operations are:

* transfer_operation
* limit_order_create_operation
* asset_issue_operation

Full list
https://github.com/cryptonomex/graphene/blob/master/libraries/chain/include/graphene/chain/protocol/operations.hpp


.. _confidential-transactions-guide:

.. include:: confidential-transactions.rst

------------

.. _mannually-construct-transaction:

.. include:: construct-transaction.rst

---------------

.. _proposing-transaction:

.. include:: propose-transaction.rst

----------------------------

.. _cli-guide:


CLI Wallet - FAQ 
=================

.. _howto-prepare-cli-wallet-for-trading:

.. _preparing-cli-wallet-trading:

.. include:: preparing-cli-wallet-trading.rst


---------------

.. _cli-wallet-usage:

.. include:: cli-wallet-usage.rst

--------------

.. _transfering-funds-cli-wallet:

.. include:: transfer-funds-cli.rst

--------------
 
.. _cli-faq:
 
.. include:: cli-faq.rst

----------------

.. _lifetime-membership-create:

Lifetime Membership
====================

Create lifetime membership
-------------------------

How can I create, register and upgrade an account to Lifetime Membership?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Without already having an account, or knowing someone that has an
account, it is not possible. You can't create accounts out of nowhere.

But you can work around it by importing an active key of a **Lifetime
Member** account that has funds:

1. In the gui, go to the permissions tab of an account that is funded
   and has a LTM status.
2. Click on the BTS public key on the ACTIVE tab and copy the private
   key.
3. In the cli-wallet run:  
   `import_key <account_name> <private_key>`
4. Then run:  
   `suggest_brain_key`  
   and copy the brain key. (You might want to make a backup of your
   brain key somewhere.)
5. Create a new account with this command:  
   `create_account_with_brain_key <brainkey> <new_account_name> <imported_name> <imported_name> true`

This will create a new account called `<new_account_name>` and set the
registrar and referrer to `<imported_name>`.  The brainkey can be used
to regenerate the account (even in the GUI wallet).  You can manually
delete the other active key from the `wallet.json` file.



.. _witness-guide:

Witness - FAQ
===============

.. _howto-run-witness: 

.. include:: witness-howto.rst

----------

.. _howto-become-active-witness:

.. include:: witness-create.rst

-----------

.. _change-witness-key:

.. include:: witness-change-key.rst  

------------

.. _witness-faq:

.. include:: witness-faq.rst

-------------


.. _committee-guide:   
   
Committee Guide
===================

.. _committee-create:

.. include:: committee-member-create.rst

------------

.. _committee-fee-change:

.. include:: committee-fee-change.rst

------------

.. _committee-approve-proposal:

.. include:: committee-approve-proposal.rst  

-----------

.. _committee-propose-action:

.. include:: committee-propose-action.rst

----------

.. _worker-guide:
   
Worker Guide
==================

.. _worker-create:

.. include:: worker-create.rst

------------

.. _worker-budget:

.. include:: worker-budget.rst
  

------------

Migrating from BitShares 1.0
===============================


.. _howto-exporting-wallet:

.. include:: howto-exporting-wallet.rst
  

.. _howto-importing-wallet:

.. include:: howto-importing-wallet.rst
  
.. _migration-remarks:

.. include:: howto-importing-wallet-remarks.rst
  




|

