
.. _dev-faq:

Developers - FAQ
==================
  
.. _dev-faq1:

Are there any sharding mechanics currently deployed?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

No

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
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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


.. _dev-faq4:

How are SPV clients handled?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

No SPV clients at the moment, each full node can expose a public websocket/http api.


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

.. _dev-faq6:

How do new clients bootstrap into the network?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Trusted seed nodes. Knowledge of initial witness keys.

.. _dev-faq7:

How is accounting addressed in Bitshares? Is it a Nxt style accounting model or like Bitcoin's UTXO
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Each account has a finite set of balances, one for each asset type.

.. _dev-faq8:

How is time addressed in the blockchain? Is NTP used or some other protocol?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

NTP

.. _dev-faq9:

Is the scripting language turing complete?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

No scripting

.. _dev-faq10:
	   
Is there a specification for Bitshares scripting language? (assuming there is one)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

No scripting

.. _dev-faq11:

Is this done via a gossip protocol or through a federate relay?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each node immediately broadcast the data it receives to its peers after validating it
https://github.com/cryptonomex/graphene/blob/master/libraries/p2p/design.md


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

	   
.. _dev-faq13:	   
	   
What is the average block time?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Current 3 seconds, configurable by chain parameters.

	   
.. _dev-faq14:
	   
What is the average size in Bytes of a Bitshares transaction?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Average wire size of operations is ~30 bytes.
Average mem size of operations is ~100 bytes.
https://github.com/cryptonomex/graphene/blob/master/programs/size_checker/main.cpp


  
.. _dev-faq17:
  
What is the standard Bitshares address structure and format?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

address = 'BTS'+base58(ripemd(sha512(compressed_pub)))  (checksum obviated)
But addresses are not used directly, instead you have an account (that can be controlled by one or more address, pubkey or another account).
https://bitshares.org/technology/dynamic-account-permissions/

.. _dev-faq18:

What public key system is used? If elliptic curve, then what is the curve?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Same as Bitcoin, secp256k1.

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





|

