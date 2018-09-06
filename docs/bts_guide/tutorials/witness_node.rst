
.. _howto-become-active-witness:

*********************************
Become an Active Witness
*********************************

.. contents:: Table of Contents
   :local:
   
--------------------

A Block-Producing Witness
===========================


Requirements
----------------

- A registered account in the corresponding network
- Some funds in the account to pay for the registration fee
- Executable binary
- Lifetime Member (LTM) status

Technical Specifications
---------------------------

- :ref:`node_system_requirements` 
   

Active Witness Duties
------------------------

- Be a reliable blockproducer
- Maintain a public seednode
- Publish accurate, frequently updated (check 1-2 times per hour), price feeds for the Smart Coins & Market Pegged Assets

---------------------------

How to become a Block-Producing Witness
============================================================

Steps
------------

1. Run the Witness/Full Node on the Network
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 We first run the witness node *without block production* and connect it to the P2P network with the following command:: 

    $ programs/witness_node/witness_node --rpc-endpoint 127.0.0.1:8090

 This command opens a RPC port 8090 for *localhost* so that we can later connect the CLI wallet with it. After the network was synced and periodically receives new blocks from other participants, we can go on to the next step.

2. Create a CLI Wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
 
 We now open up the cli_wallet. The following command connects to our plain Witness (Fill) node::

    $ programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090

 After launched the wallet successfully, first thing to do is setting up a password for the newly created wallet prior to importing any private keys:

- ``set_password``::

    new >>> set_password <password>
    set_password <password>
    null
    locked >>>

- ``unlock``::

    locked >>> unlock "<password>"
    unlock "<password>"
    null
    unlocked >>>

 Wallet creation is now done.

3. Import your Account (and funds) into CLI Wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 To gain access to Blockchain, we import the account name (owner key) and the balance containing (active key) into the CLI wallet:

- ``import_key`` (owner wifkey), ``import_key`` (active wifkey), ``list_my_accounts`` , ``list_account_balances``::

    >>> import_key <accountname> <owner wif key>
    true
    >>> import_key <accountname> <active wif key>
    true
    >>> list_my_accounts
    [{
        "id": "1.2.15",
        ...
        "name": <accountname>,
        ...
    ]
    >>> list_account_balances <accountname>
    XXXXXXX BTS

 Both keys can be exported from the web wallet.

4. Upgrade your Account to a Lifetime Member
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- ``upgrade_account``

 Since **only lifetime members can become witnesses**, you must first upgrade to a lifetime member. This step costs the lifetime-upgrade fee::

    >>> upgrade_account <accountname> true
    [a transaction in json format]

5. Registering a New Witness (become a witness)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 To become a witness and be able to produce blocks, you first need to create a witness object that can be voted in.

- ``create_witness``::

    >>> create_witness <accountname> "http://<url-to-proposal>" true
    {
      "ref_block_num": 139,
      "ref_block_prefix": 3692461913,
      "relative_expiration": 3,
      "operations": [[
      21,{
        "fee": {
          "amount": 0,
          "asset_id": "1.3.0"
        },
        "witness_account": "1.2.16",
        "url": "url-to-proposal",
        "block_signing_key": "<PUBLIC KEY>",
        "initial_secret": "00000000000000000000000000000000000000000000000000000000"
      }
    ]
      ],
      "signatures": [
          "1f2ad5597af2ac4bf7a50f1eef2db49c9c0f7616718776624c2c09a2dd72a0c53a26e8c2bc928f783624c4632924330fc03f08345c8f40b9790efa2e4157184a37"
      ]
    }

 Our witness is registered, but it can’t produce blocks because nobody has voted it in. You can see the current list of active witnesses by the following command:

- ``get_global_properties``::

    >>> get_global_properties
    {
      "active_witnesses": [
    "1.6.0",
    "1.6.1",
    "1.6.2",
    "1.6.3",
    "1.6.4",
    "1.6.5",
    "1.6.7",
    "1.6.8",
    "1.6.9"
      ],
      ...


6. Upvote the Witness 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 Now, we should vote our witness in. Vote all of the shares your account `<accountname>` in favor of your new witness.

- ``vote_for_witness``::

    >>> vote_for_witness <accountname> <accountname> true true
    [a transaction in json format]

 .. Note:: If you want to experiment with things that require voting, be aware that votes are only tallied once per day at the maintenance interval. ``get_dynamic_global_properties`` tells us when that will be in ``next_maintenance_time``. Once the next maintenance interval passes, run ``get_global_properties`` again and you should see that your new witness has been voted in.


 We need to wait until the next maintenance interval until we can see votes casted for our witness.

7. Configuration of the Witness Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 Get the witness object using ``get_witness`` and take note of two things. 
 
 :`id`:  (i.e. `witness-id`)  is displayed in `get_global_properties` when the witness is voted in, and we will need it on the witness_node command line to produce blocks
 :`signing_key`:  (i.e. `private-key`) the public signing_key, we can look up the corresponding private key.  


- ``get_witness``:: 

    >>> get_witness <accountname>
    {
      [...]
      "id": "1.6.10",
      "signing_key": "GPH7vQ7GmRSJfDHxKdBmWMeDMFENpmHWKn99J457BNApiX1T5TNM8",
      [...]
    }

 Once we have both values, run ``dump_private_keys`` which lists the *public-key private-key pairs* to find the *private key*.

 **Warning**  ``dump_private_keys`` will display your keys unencrypted on the terminal, don’t do this with someone looking over your shoulder.


- ``dump_private_keys``::

    >>> dump_private_keys
    [[
      ...
      ],[
    "GPH7vQ7GmRSJfDHxKdBmWMeDMFENpmHWKn99J457BNApiX1T5TNM8",
    "5JGi7DM7J8fSTizZ4D9roNgd8dUc5pirUe9taxYCUUsnvQ4zCaQ"
      ]
    ]

 **Now we need to start the witness, so shut down the wallet (ctrl-d), and shut down the witness (ctrl-c).** 

 Re-launch the witness, now mentioning the new witness 1.6.10 and its keypair::

    ./witness_node --rpc-endpoint=127.0.0.1:8090 \
                   --witness-id '"1.6.10"' \
                   --private-key '["GPH7vQ7GmRSJfDHxKdBmWMeDMFENpmHWKn99J457BNApiX1T5TNM8", "5JGi7DM7J8fSTizZ4D9roNgd8dUc5pirUe9taxYCUUsnvQ4zCaQ"]'

 Alternatively, you can also add this line into your ``config.ini``::

    witness-id = "1.6.10"
    private-key = ["GPH7vQ7GmRSJfDHxKdBmWMeDMFENpmHWKn99J457BNApiX1T5TNM8","5JGi7DM7J8fSTizZ4D9roNgd8dUc5pirUe9taxYCUUsnvQ4zCaQ"]

.. Note::  Make sure to use YOUR public/private keys instead of the once given above!

If you monitor the output of the ``witness_node``, you should see it generate blocks signed by your witness:

::

    Witness 1.6.10 production slot has arrived; generating a block now...
    Generated block #367 with timestamp 2015-07-05T20:46:30 at time 2015-07-05T20:46:30
		
|


|