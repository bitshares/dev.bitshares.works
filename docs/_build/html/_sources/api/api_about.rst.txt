
.. _api-guide:

API Guide
========================

BitShares APIs are available by using Remote Procedure Calls (RPC) and WebSocket Calls & Notifications. All API calls are formatted in JSON and return JSON only. We look into the APIs' every feature. BitShares-Core API references locate in the `Doxygen documentation. <https://bitshares.org/doxygen/index.html>`_  Also, you can find the APIs' classes, components, and elements information looking into "Graphene - Namespaces" section. 

.. contents:: Table of Contents
   :local:
   
------------
   

APIs Categories
------------------------------

APIs are separated into two categories, namely

* **Blockchain APIs** -- It is used to query blockchain data (account, assets, trading history, etc). Besides data stores in the blockchain itself (blocks, transactions, etc. ..), higher level objects (such as accounts, balances, etc. …) can be retrieved through the full node’s database.

* **Wallet API** -- It interact with the blockchain to operate. 
The wallet (cli-wallet)  has your private keyss and signing capabilities.  It requires a running full node (witness node) (not necessarily locally) and connects to it. Because the wallet does not offer P2P or blockchain capabilities directly.

In contrast to many existing ecosystems, there is no centralized service that lets you access private API calls after successful authentication. Instead, your run your wallet (and optionally a full node) **locally** and are with **your own API service provider**. This obviously has the advantage that you don’t need to give access to your funds to any third party but has the slight disadvantage that you need to run a local `wallet application`, that however does not download the whole blockchain for verification. If you run a sensitive business, we recommend to also run a local full node to download and verify the blockchain and interface your wallet with your local full node.


-----------------

Interfacing with Graphene
------------------------------

The set of available calls depends on whether you connect to a full node (``witness_node``) or the wallet (``cli_wallet``). Both support RPC-JSON. The full node also supports the websocket protocol with notifications

Which blockchain network you connect to depends on the configuration of the full node and the wallet. If you run a full node, we recommend to connect your wallet to your local full node even though it could be connected to any other public full node as well


.. image:: api-interfacing-graphene.png
        :alt: BitShares
        :width: 600px
        :align: center
		
		
For sensitive businesses that want to ensure that deposits are irreversible, we recommend the use of the :ref:`High Security Setup <secure-network-configuration>`. That contains a **delayed node** to pass only irreversible transactions to the API.

.. Note:: All API calls are formated in JSON and return JSON only.


------------------------------------

Quick points - *Side notes*
---------------------------

**Blockchain APIs - introduction**

  The blockchain API can be used to obtain any kind of data stored in the blockchain. Besides data stores in the blockchain itself (blocks, transactions, etc. ..), higher level objects (such as accounts, balances, etc. …) can be retrieved through the full node’s database.

  It is not required to run a local full node if you want to query a particular blockchain or database, but you can also query any existing public node for information.

  * Database API
  * Account History API
  * Crypto API
  * Network Broadcast API
  * Network Nodes API

**Wallet API Calls - introduction**

  Wallet API to watch deposits of users into the exchange’s account. Because the delayed node only knows about irreversible blocks all transactions are at this point irreversible as well. For customer withdrawals, we will interface with the wallet to initiate transfers to the accounts of the customers on request. As we are connected to the trusted node directly, there will not be any delay on withdrawals.

  Wallet specific commands, such as transfer and market orders, are only available if connecting to cli_wallet because only the wallet has the private keys and signing capabilities and some calls will only execute if the wallet is unlocked.


**API Calls**

  * All API calls are formatted in JSON and return JSON only. 
  * The set of available calls depends on whether you connected to a full node (witness_node) or the wallet (CLI-wallet). Both support RPC-JSON.   
  * The full node also supports the websocket (statefull) protocol with notifications.
  * The full node offers a set of API(s), of which only the database calls are available via RPC. Calls that are restricted by default (i.e. ``network_node_api``) or have been restricted by configuration are not accessible via RPC because a statefull protocol (websocket) is required for login.
  * The CLI- wallet can open a RPC port so that you can interface your application with it. You have the choices of 

    - websocket RPC via the ``-r`` parameter, and 
    - HTTP RPC via the ``-H`` parameter.

**Call methods**

  * Remote Procedure Calls -- In Graphene, RPC calls are **state-less** and accessible via regular JSON formated RPC-HTTP-calls.
  * Websocket Calls -- In Graphene, Websocket calls are **stateful** and accessible via regular JSON formated websocket connection. 

**API Access Restrictions**

  The full node offers a set of API(s), of which only the database calls are avaiable via RPC. Calls that are restricted by default (i.e. network_node_api) or have been restricted by configuration are not accessible via RPC because a statefull protocol (websocket) is required for login.

**Network and Wallet Configuration**

  * General Network and Wallet Configuration
  * Secure Network and Wallet Configuration

**Blockchain Objects and their Identifiers**

  In the protocol space, there are raw objects such as, accounts, assets, committee members as well as orders, proposals and balances. The implementation space is used to gain access to higher abstraction layers for instance content of the current database state (these include, current global blockchain properties, dynamic asset data, transaction histories as well as account statistics and budget records).


|

|
