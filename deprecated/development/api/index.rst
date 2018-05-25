
.. _api-guide:

API Guide
*************************************************

APIs are separated into two categories, namely

* the **Blockchain API** which is used to query blockchain data (account, assets, trading history, etc.) and 
* the **Wallet API** which has your private keys loaded and is required when interacting with the blockchain with new transactions.

.. note:: In order to interface with *the wallet*, you need to run the
          :ref:`cli-wallet`. Neither the
          **light-wallet**, nor the **web wallet** will provide
          you with an API.

In contrast to many existing ecosystems, there is no centralized services that
let's you access private API calles after successful authentication. Instead,
your run your wallet (and optionally a full node) **locally** and are with
**your own API service provider**. This obviously has the advantage that you
don't need to give access to your funds to any third party but has the slight
disadvantage that you need to run a local :ref:`wallet appilcation
<cli-wallet>`, that however does not download the
whole blockchain for verification. If you run a sensitive business, we
recommend to also run a local full node to download and verify the
blockchain and interface your wallet with your local full node.

----------

Interfacing with Graphene
==========================

The set of available calls depends on whether you connect to a full node
(``witness_node``) or the wallet (``cli_wallet``). Both support RPC-JSON. The
full node also supports the websocket protocol with notifications.

Which blockchain network you connect to (BitShares, MUSE, ..) depends on the
configuration of the full node and the wallet. If you run a full node, we
recommend to connect your wallet to your local full node even though it could
be connected to any other public full node as well.


.. image:: api-interfacing-graphene.png
        :alt: API Interfacing with Graphene
        :width: 650px
        :align: center

|

For sensitive businesses that want to ensure that deposits are irreversible, we
recommend the use of the :ref:`High Security Setup <high-security-network-setup>`.
That contains a *delayed node* to pass only irreversible transactions to the API.


|

.. _rpc:

.. include:: rpc.rst

.. _websocket-calls:

.. include:: blockchain_api/websocket.rst

.. _api-access-restrictions:

.. include:: blockchain_api/access.rst

.. _blockchain-api:

Blockchain API(s)
==================

The blockchain API can be used to obtain any kind of data stored in the blockchain. Besides data stores in the blockchain itself (blocks, transactions, etc. ..), higher level objects (such as accounts, balances, etc. ...) can be retrieved through the full node’s database.

It is not required to run a local full node if you want to query a particular blockchain or database, but you can also query any existing public node for information.

|

The Graphene full node distinguishes several different APIs that can be
accessed as described :ref:`the websockets documentation <websocket-calls>`.

Access to some APIs may be **restricted** and requires login with username and
passphrase. More detailed description about this can be found on the
:ref:`api-access-restrictions` page.

|

.. _database-api:

.. include:: blockchain_api/database.rst

----------

.. include:: blockchain_api/history.rst

.. include:: blockchain_api/crypto.rst

|

.. include:: blockchain_api/network_broadcast.rst

.. include:: blockchain_api/network_node.rst

.. _wallet-api-calls:

.. include:: wallet-api.rst   
   
.. _blockchain-objects-ids:

.. include:: blockchain-objects-ids.rst

.. _often-used-calls:
 
.. include:: often-used-calls.rst   
     
 
|   
   
   