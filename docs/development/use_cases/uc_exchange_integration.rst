.. _usecase-exchanges:

*************************************
Exchanges, Bridges, and Gateways
*************************************

**Exchange**, **Bridges**, and **Gateways** represent business that trade or
exchange assets that are located **inside** a Graphene network (e.g. BitShares)
against assets that are **located** outside the blockchain network. For
instance, exchanges trade ``BTS:BTC`` while bridges exchange ``bitBTC:BTC``.

We here illustrate the steps necessary to securely operate as exchange or
gateway. Gateways take Fiat and convert them to their corresponding bitAsset at
a fee and vice versa. For instance:

1. A customer requests 100 bitUSD from a gateway
2. The gateway sends an invoice with bank account details
3. When the funds arrive at the gateway a percentage is taken as a fee and the
   rest is transfered as bitUSD directly into the BitShares wallet of the
   customer.

For exchanges we recommend to also read :ref:`what-is-different` and
:ref:`often-used-calls`.

----------

**Integration Instructions**

.. contents:: Table of Contents
   :local:
   
---------------

Step-By-Step Instructions for Exchanges
============================================

We here describe how to interface your exchange with BitShares step-by-step. We will link to a more detailed description where appropriate.

1.Installation
----------------

In this step-by-step instruction we assume you have successfully built from the sources. If you've not done yet, see the :ref:`instruction <installation>`.

2. Running Daemons and Wallet
------------------------------

For security reasons we will run two daemons and a wallet according to these diagram:

.. image:: exchange-demons.png
        :alt: BitShares 
        :width: 600px
        :align: center
		
		
In this tutorial we will run all deamons and the wallet on the same machine and use different ports to distinguish them::

* port ``8090``: trusted full node
* port ``8091``: delayed node
* port ``8092``: wallet

Read more details :ref:`network-setups`.

3. Trusted Full Node
---------------------

The trusted full node is your entry point to the BitShares P2P network. It will hold the blockchain, connect to other peers, and will receive new blocks in *real-time*.::

    ./programs/witness_node/witness_node --data-dir=trusted_node/ --rpc-endpoint="127.0.0.1:8090"

.. Note:: Until the genesis block is integrated into the binary/souces, you may additionally need to download the genesis block from github and add the parameter ``--genesis-json <genesis.json>``.

.. Note:: Unless the seed nodes are encoded into the binary, you may need to add a known seed node with ``-s xxx.xxx.xxx.xxx:yyy`` in order to initially connect to the P2P network. (See `Release Page <https://github.com/bitshares/bitshares-core/releases>`_)

> **Note:** To start a node with reduced RAM please see :ref:`Memory reduction for nodes <memory-nodes>`.

Delayed Node
--------------

The delayed full node node will provide us with a delayed and several times confirmed and verified blockchain. All transactions that are confirmed by the delayed node are **irreversible**.::

    ./programs/witness_node/witness_node --trusted-node="127.0.0.1:8090" \
                                         --rpc-endpoint="127.0.0.1:8091"
                                         -s "0.0.0.0:0" \
                                         --p2p-endpoint="0.0.0.0:0" \
                                         --seed-nodes "[]"

We will use this node for notifications of customer deposits.

Wallet
-------------

The wallet will be used to transfer assets to the customers. It connects to the trusted full node and has spending privileges for the hot wallet.::


    ./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://127.0.0.1:8090" \
                                     --rpc-http-endpoint="127.0.0.1:8092"

|
								 
--------------------

Query Blockchain for Required Data
===================================

We now use the open ``cli_wallet`` to issue transfers and query the
blockchain for more information. All cli-wallet commands can be found
when clicking the tabulator twice.

First of all, we create a new wallet
and set a pass phrase:::

    >>> set_password <password>

This will generate a ``wallet.json`` file for you that can contain
encrypted private keys to your account.

Import the active key into BitShares 2 wallet:::

    BitShares 2: >>> import_key <account-name> <active_private_key>

This gives access to the funds stored in ``<account-name>``. We will
need the memo private key later when watching deposits.


Blocks
------

Raw blocks can be read when running:::

    >>> get_block <block_number>

The output will contain a field with all ``transactions``. Each
transaction is user-signed and can potentially contain multiple
**operations**. A transaction with a single transfer operation (``0``)
takes the form:::

     {'expiration': '2018-10-15T13:28:28',
      'extensions': [],
      'operations': [[0,
                      {'amount': {'amount': 100000, 'asset_id': '1.3.0'},
                       'extensions': [],
                       'fee': {'amount': 10420, 'asset_id': '1.3.0'},
                       'from': '1.2.282',
                       'to': '1.2.0'}]],
      'ref_block_num': 59307,
      'ref_block_prefix': 2643469633,
      'signatures': ['2030ec0825f18d180723a11369b213bc1758d351f523572549d0f10c5d3fe88b1a6ad946b700ebeafa867b15180af588088d581a4c3cb350095dafa87123c8f125']}


The operation payload, in this case, is:::

     {
         'fee': {
                'amount': 10420,
                'asset_id': '1.3.0'},
         'amount': {
                'amount': 100000,
                'asset_id': '1.3.0'},
         'memo': {
                'from': 'BTS5TPTziKkLexhVKsQKtSpo4bAv5RnB8oXcG4sMHEwCcTf3r7dqE',
                'message': '58a8a515041812071b2402a0bf67c5e8',
                'nonce': 3324169168288624499,
                'prefix': 'BTS',
                'to': 'BTS5TPTziKkLexhVKsQKtSpo4bAv5RnB8oXcG4sMHEwCcTf3r7dqE'},
         'extensions': [],
         'from': '1.2.282',
         'to': '1.2.0'
     }

We can ignore ``extensions``.
The ``from`` and ``to`` field define the account ids. You can identify
the account ids from account names by using ``get_account <id or name>``.
The ``fee`` and ``amount`` fields are an *asset* and thus contain an
**integer** amount (think: satoshis) and an asset id. The precision to
get from integer representation to floats can be obtained with
``get_asset <asset name or id>``. With that number, the floating amount
can be obtained by:::

     amount_integer / 10 ** precision

In the case above, the asset with id 1.3.0 is ``BTS`` which has
precision 5. Hence, the floating number transfered from account 1.2.282
to 1.2.0 is 1.0000 BTS!

The optional memo is used to send an encrypted message along the
transfer. To decode the memo, the receivers or senders private key (for
the public keys in the memo) need to be in the cli-wallet's wallet. If
that is the case, then the memo can be decoded using:::

    >>> read_memo {..json..}

It is recommended to call those methods via API (see ``rpc-endpoint``
above).

------------------

- Watching Deposits with Python

 For watching deposits, we recommend pybitshares' *Notify* module. The full documentation is available on  `pybitshares.com <http://pybitshares.com>`_

- Executing Transfers for Withdrawals

 For transfering funds, we recommend pybitshares. This python module enables all features required to operated on/with BitShares. The full documentation is available on `pybitshares.com <http://pybitshares.com>`_.
