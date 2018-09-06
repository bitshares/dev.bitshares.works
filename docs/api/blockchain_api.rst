

.. _blockchain-api:

***********************
Blockchain API(s)
***********************

The blockchain API can be used to obtain any kind of data stored in the blockchain. Besides data stores in the blockchain itself (blocks, transactions, etc. ..), higher level objects (such as accounts, balances, etc. ...) can be retrieved through the full node’s database.

It is not required to run a local full node if you want to query a particular blockchain or database, but you can also query any existing public node for information.

---------


The Graphene full node distinguishes several different APIs that can be
accessed as described :ref:`the websockets documentation <websocket-calls>`.

Access to some APIs may be **restricted** and requires login with username and
passphrase. More detailed description about this can be found on the
:ref:`api-access-restrictions` page.


BitShares-Core API References - Doxygen Documentation *\<Quick links\>*
   - `BitShares-Core Doxygen (Main)  <https://bitshares.org/doxygen/index.html>`_
   - `Class Hierarchy order <https://bitshares.org/doxygen/hierarchy.html>`_


----------

.. toctree::
   :maxdepth: 3

   blockchain_api/database
   blockchain_api/history
   blockchain_api/crypto
   blockchain_api/network_broadcast
   blockchain_api/network_node


|

|

