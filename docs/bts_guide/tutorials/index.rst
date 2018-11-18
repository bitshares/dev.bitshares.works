
.. _dev-guides:

*****************************
Developer Guide (Tutorials)
*****************************

This section intended to collect “How To” steps information to show some examples to perform a process.

-----------------

Accounts
===============


* :ref:`How can I register an account by using CLI? <create-account-dev-cli>`
* :ref:`How can I change a Cloud Wallet Password? <howto-change-cloud-wallet-pwd>`
* :ref:`How can I import a GUI-wallet account into CLI-wallet <howto-import-gui-wallet-account-cli>`


**Vesting Balance**

* :ref:`How can I check a Vesting Balances? <list-vesting-balances>`
* :ref:`How can I claim a Vesting Balance? <claiming-vesting-balance>`

 
API
============

* `How an Exchange can integrate their services to the BitShares UI Wallet - Gateway Integration Requirements <https://github.com/bitshares/bitshares-ui/wiki/Gateway-Integration-Requirements>`_ : from BitShares UI wiki
* :ref:`How can I enable RPC Logging? <rpc-logging>`
* :ref:`How to script Websockets by Python <websocket-script-support>`

  
Assets
===========
* Read: User Guide `BitShartes Assets Tokens <http://how.bitshares.works/en/latest/bts_holders/tokens.html>`_

  1. User Issued Assets (UIAs)
  2. Market Pegged Assets (MPAs)
  3. Exchange Backed Assets (EBA)
  4. Privatized BitAssets
     - :ref:`Feed Producer <privbta-cli>`
  5. Fee Backed Asset
  6. Prediction Markets

	
* :ref:`How can I create a new UIA by using GUI? <creating-new-uia-gui>`
* :ref:`How can I create a UIA manually (CLI)? <uia-create-manual>`
* :ref:`How can I create a MPA manually (CLI)? <mpa-create-manual>`
* :ref:`How can I update/change an existing UIA (CLI)? <uia-update-manual>`
* :ref:`How can I publish a price feed (CLI)? <publish-feed>`

Prediction Market
------------------

* `What is a  prediction market? <http://how.bitshares.works/en/latest/bts_holders/tokens/pm.html>`_
* :ref:`How can I create a Prediction Market (CLI)? <pm-create-manual>`
* :ref:`How can I close/settle a Prediction Market (CLI)? <pm-close-manual>`


BitShares Decentralized Exchange (DEX)
=========================================

* Read: `User Guide : Dex <https://how.bitshares.works/en/latest/bts_holders/dex.html>`_

  1. Trading
  2. Short Selling BitAssets
  3. MarginCall Mechanics
  
* :ref:`How can I run own Decentralized Exchange (DEX)? <distributed-access-to-dex>`
* :ref:`How can I prepare BitShares Exchange? (Single Node Edition) <exchange-single-node>`




Committee Guide
======================

* :ref:`How can I create a New Committee Member? <committee-create>`
* :ref:`How to create a Proposal to change a fee? <committee-fee-change>`
* :ref:`How to approve/disapprove a Proposal <committee-approve-proposal>`
* :ref:`How to Propose Committee Actions <committee-propose-action>`

Delegated Proof of Stake 
============================
* Read" User Guide `(DPOS) <https://how.bitshares.works/en/latest/technology/dpos.html#>`_




.. _monitoring_support:

Monitoring
================

* :ref:`How to monitor Account Deposits - Python <monitoring-account-deposits-python>`
* :ref:`How to monitor Balance History of an Account  - NodeJS <nodejs-example>`


.. _witness-node-guide-tutorials: 
  
Node  
==============================

* :ref:`How to connect to your own Full Node (GUI) <howto-connect-own-full-node-gui>`
* :ref:`How to change the Signing Key of your Witness <change-witness-key>`
* :ref:`What is System Requirements <system-requirements-node>` 
* :ref:`How to Run and Use a Full Node <how-to-run-full-node2>`
* :ref:`How to run Node in the background by using GNU screen <manage-gun-screen>`
* :ref:`How to run a BitShares API Node <run-api-node-guide>`		
* :ref:`How to backup a server <witness-backup-server>`

.. _witness-blockproducer-guide: 

Node (Block Producer - Witness) 
====================================

* :ref:`How to Become a Block Producer <howto-become-active-witness>`
* :ref:`How to verify block production <veryfy_block_production>`
* :ref:`How to backup a server <witness-backup-server>`
* :ref:`How witness Price Feeds work <witness-price-feeds>`




Performance
===========================
* `How to run the Performance Test <https://github.com/bitshares/bitshares-core/tree/develop/tests/performance>`_

Plugin
===================

* `BitShares Plugins - ReadMe <https://github.com/bitshares/bitshares-core/tree/master/libraries/plugins>`_
* :ref:`elastic-search-plugin`

  - How to store account history data into an elasticsearch database.
  
* :ref:`memory-nodes`

  - Help to reduce RAM usage significantly by using witness_node executable options.

* `Plugin Template - (create a demo API hello) <https://github.com/bitshares/bitshares-core/blob/hello_plugin/libraries/plugins/hello/README.md>`_
* `Discussion: "Create plugin script" (#1302) <https://github.com/bitshares/bitshares-core/pull/1302>`_

  
.. include:: ../../supports_dev/lib_python.rst   
  
  
Smart Contracts
====================

* :ref:`pre-graphene-smart-contract`


Testnets
===================

* :ref:`Code Coverage Testing (wiki) <how-to-testing-bts>`
* :ref:`How to prepare a Public Testnet - Quick Start Guide <public-testnet-details>`
* :ref:`How to set up/deploy a Private Testnet (witness-node) <private-testnet-guide>`
* :ref:`How to set up a Public Testnet <public-testnet-guide>`
* :ref:`How to set up Python Library <how-to-setup-python-lib>`
* :ref:`How to do a code coverage testing <how-to-testing-bts>`
* :ref:`How to set up Faucet <how-to setup-faucet>`
* :ref:`How to set up Nignx <how-to-setup-nignx>`

Transfer / Transactions
============================


* :ref:`dev-faq14`
* :ref:`dev-faq19`
* :ref:`How Multi-signature' works <bts-multi-sign>`
* :ref:`How to use the CLI wallet to perform Confidential Transfers  <confidential-transactions-guide>`
* :ref:`Hot to Construct Any Transaction - Manually <manually-construct-transaction>`
* :ref:`How to Crafting, Proposing, and Approving a Transaction <proposing-transaction>`
* :ref:`How to use the CLI wallet to Perform Stealth Transfers in BitShares (wiki) <w-stealth-transfers>`  
* :ref:`Proposed Transaction and  Multi-Signature <proposed-tran>`

.. _wallet-cli-tutorials: 
  
Wallet / CLI
=====================

* :ref:`How to connect and use the CLI-Wallet <run-cli-wallet-steps>`
* :ref:`How to setup Network and Wallet Configuration <network-setups>`
* :ref:`How to transferring Funds by using the CLI-Wallet <transfering-funds-cli-wallet>`
* :ref:`How to import a GUI-Wallet Account into CLI-Wallet <howto-import-gui-wallet-account-cli>`
* `Where to find BitShares Public Full Nodes <https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js>`_


Worker 
=======================

* :ref:`How to Create a Worker <worker-create>`
* :ref:`How to find claim Worker Pay <worker-budget>`
* `How the Blockchain Worker System work? <https://bitshares.org/doxygen/group__workers.html>`_ (*open a doxygen documentation)



Migration (from BitShares1.0)
==================================

* :ref:`Explore Your Wallet <howto-exporting-wallet>`
* :ref:`Improve Your Wallet <howto-importing-wallet>`
* :ref:`migration-remarks`



|

----------------------

 