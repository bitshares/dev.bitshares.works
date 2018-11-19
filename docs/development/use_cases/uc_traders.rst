.. _usecase-traders:

***********
Traders
***********

**Traders** make use of the API provided to interact with a BitShares network and provide market makers and liquidity. The APIs can be easily used to implement automated robots for trading algorithms.

Blockchain-based decentralized exchanged (DEX) are slightly different to centralized exchanges and as a consequence, dealing with the DEX programmatically via APIs differs from centralized approaches as well.
However, our developers have put quite some efforts into making the DEX as easy to use as their centralized counterparts and offer an API to the **public exchange data** that is very similar. However, **private
exchange APIs** are different due to the fact that no entity except yourself can access your funds. For this reason, trading in the DEX requires that you have the private key to your account installed in an
application that can construct and sign the corresponding transactions for you. One of these applications is the cli-wallet which, after installation and configuration, offers a **your own private
API**.

For exchanges we recommend to also read...

* :ref:`CLI Wallet and the Connectivity <cli-wallet-intro>` 
* `What is different BitShares <http://how.bitshares.works/en/latest/technology/difference_bitshares.html>`_
* :ref:`often-used-calls`
* `User Guide : BitShares Decentralized Exchange (DEX) <https://how.bitshares.works/en/latest/bts_holders/dex.html>`_

  1. `Trading <https://how.bitshares.works/en/latest/bts_holders/dex_trading.html>`_
  2. `Short Selling BitAssets <https://how.bitshares.works/en/latest/bts_holders/dex_short.html>`_ 
  3. `MarginCall Mechanics <https://how.bitshares.works/en/latest/bts_holders/dex_margin_mechanics.html>`_ 

   
----------
  
Public API
=================================================================

The best way to get public data on markets is via websocket connection
to a public full-node which provides traders with

* a ticker
* order books
* trade history
* and more.

Here is a latest list of `BitShares Public Full Nodes <https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js>`_ 

A detailed description of how to interface with Graphene-based blockchain (e.g. BitShares) and a list of available calls can be found here: 
 
* :ref:`websocket-calls`

* :ref:`blockchain-api`

-------------------------

   
Private API
=================================================================

As briefly mentioned above, trading in the DEX programmatically requires that you run your own cli-wallet. The following guides gives a brief introduction on how to use the CLI wallet and configure it properly so that it can be used as a **private API server**.

* :ref:`Launch a CLI Wallet <run-cli-wallet-steps>`


After installation and configuration of the private API, we can use RPC to create orders, cancel orders, create and adjust call orders, and more.  The CLI wallet offers a wide range of calls that can be used to
mange your account and trade in the DEX.

* :ref:`wallet-api-calls` 
* :ref:`CLI Wallet Guide <wallet-cli-tutorials>` 
   

Libraries have been developed that simply the interaction with both, the full node and the CLI wallet and make interactions with the blockchain and the DEX very easy.

* :ref:`support-and-optim`.



|

|

