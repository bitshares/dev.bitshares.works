
.. _support-and-optim:


***************************
Software & References
***************************

.. contents:: Table of Contents
   :local:
   
-------

Software
=========================

BitShares Core
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0

   * - `Bitshares Core <https://github.com/bitshares/bitshares-core>`_ 
     - BitShares Core is the BitShares blockchain implementation and command-line interface	 	 
   * - `Docker Container <https://github.com/bitshares/bitshares-core/blob/master/README-docker.md>`_ 
     - Built-in Dockerfile to support docker containers.    
	 
|
	 
BitShares Core C++ Library
---------------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0	 
	 
   * - `Bitshares FC <https://github.com/bitshares/bitshares-fc>`_ 
     - Fast-compiling C++ library  provides a set of utility libraries useful for the development of asynchronous libraries	 
   * - `Websocket pp <https://github.com/bitshares/websocketpp>`_  
     - C++ websocket client/server library 	

|

HTLC
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0	 
	
   * - `Hashed Time-Lock Contracts (HTLC) on BitShares <https://github.com/bitshares/bitshares-core/wiki/HTLC>`_  
     - TESTNET: User Guide for Hashed Time-Lock Contracts (HTLC) on BitShares	 
   * - `Hashed Time-Lock Contracts (HTLC) on BitShares:CN <https://github.com/bitshares/bitshares-core/wiki/HTLC-(CN)>`_  
     - 测试网：BitShares 哈希时间锁合约（HTLC）用户指南
 
 
|

API Related 
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0	 
	
   * - `Bitshares Explorer API <https://github.com/bitshares/bitshares-explorer-api>`_  
     - REST API for BitShares 	 

 
|

Python
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0

   * - `Python Bitshares <https://github.com/bitshares/python-bitshares>`_  
     - Fully featured client-side library for the BitShares Blockchain - written entirely in python.	 
   * - `uptick <https://github.com/bitshares/uptick>`_  
     - Python-based CLI tool set for BitShares blockchain	
   * - `Faucet - Tapin <https://github.com/xeroc/tapin>`_
     -  Tapin is a python-based faucet for Graphene-based blockchains (e.g. BitShares). 


|
  
JavaScript
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0

   * - `Bitshares JS <https://github.com/bitshares/bitsharesjs>`_  
     - JavaScript tools for BitShares Encryption and Serialization. https://bitsharesjs.bitshares.org/
   * - `Bitshares JS-WS <https://github.com/bitshares/bitsharesjs-ws>`_  
     - JavaScript websocket interface for Bitshares

|
	 
BitShares UI
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0	 		 
	 
   * - `Bitshares UI <https://github.com/bitshares/bitshares-ui>`_  
     - Graphical User Interface for Bitshares
   * - `Bitshares UI API <https://github.com/bitshares/bitshares-ui-api>`_  
     - RESTful API server for Bitshares based on express-es6-rest-api	 
   * - `develop.bitshares.org <https://github.com/bitshares/develop.bitshares.org>`_  
     - Bleeding edge hosted wallet off the develop branch of bitshares-ui
   * - `reconnecting-websocket <https://github.com/bitshares/reconnecting-websocket>`_  
     - A small decorator for the JavaScript WebSocket API that automatically reconnects 		 
|	
	 	 
Mobile
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0
 
   * - `Bitshares Mobile App <https://github.com/bitshares/bitshares-mobile-app>`_  
     - This is the mobile app for bitshares blockchain 	 	 

|
	 
Other Related Software 
----------------------

.. list-table::
   :widths: 15 80
   :header-rows: 0
 
   * - `G-Wallet <https://github.com/bitshares/gwallet>`_  
     - G-Wallet is a Bitshares desktop(Linux, Windows, MAC) wallet program written in C++ using wxWidgets.  	
   * - `beet <https://github.com/bitshares/beet>`_  
     -  Beet is a stand-alone key/identity-manager and signing app for BitShares, heavily influenced by Scatter	 
   * - `faucet <https://github.com/bitshares/faucet>`_  
     - 
   * - `secp256k1-zkp <https://github.com/bitshares/secp256k1-zkp>`_  
     - Experimental fork of libsecp256k1 with support for pedersen commitments and range proofs
   * - `btsgo <https://github.com/bitshares/btsgo>`_  
     - 

 
|


--------

References
=========================

BitShares Core
--------------------

* `Bitshares Core: Doxygen Document <https://open-explorer.io/doxygen/fc/>`_ 

* `BSIPs <https://github.com/bitshares/bsips>`_  - BitShares Improvement Proposals and Protocols	 
	
* `Bitshares FC: Doxygen Document <https://open-explorer.io/doxygen/fc/>`_ 

|

API Related 
----------------------

* :ref:`explorer-wrappers`

  - For Mainnet and Tesenet: ES Wrapper to query the Bitshares data. *Try it out!*
  - Check the BitShares blockchain health. 

* `BitShares Explorer REST API - Installation Guide <https://github.com/oxarbitrage/bitshares-explorer-api#bitshares-explorer-rest-api>`_

  - Step by step on everything needed to have your own BitShares Explorer API up and running for a production environment.
	 
|
	 
Plugins
----------------------

* `BitShares Plugins List: Github ReadMe <https://github.com/bitshares/bitshares-core/blob/master/libraries/plugins/README.md>`_ 
  
  - ``account_history``, ``debug_witness``, ``delayed_node``, ``elasticsearch``, ``es_objects``,  ``grouped_orders``, ``market_history``, ``snapshot``, ``witness``


|


Testing Tool(s)
----------------------  
  
* :ref:`what-if-test`

  - The debug_node is a tool to allow developers to run many interesting sorts of "what-if" tests using state from a production blockchain. 

|

Python
----------------------
     
* `Python Bitshares Documentation <http://docs.pybitshares.com/en/latest/>`_ 

* :ref:`how-to-setup-python-lib` (also, How to Create MPAs/UIAs with Python)
 
* :ref:`monitor-account-python` - (How to setup)

* :ref:`websocket-script-support`  - (websocket-client interact with the Core API)

|

JavaScript
----------------------
	 
* :ref:`monitor-balance-nodejs`  

  - This nodejs script monitors the balance history of an account in a graphene-based network
 
 
| 