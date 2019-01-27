
**************
Testnets
**************

BitShares offers an Open Public Testnets that has been deployed and is fully functional for anyone to join and test without using real BTS.  Instead, you will receive Testnet Core asset TEST.  The Open Public Testnets is a great space to start learning about BitShares-Core features and BitShares-UI wallet. 

For the BitShares Testnet preparation, you should download a **testnet** branch from the BitShares-Core repository. The testrnet branch files are for the Testnets to use.

**The testnet branch files:** include bug fixes or new features

-------------

Public Testnets
-------------------------

If you want to prepare a Public Testnet, you can simply download and use BitShares-Core's testnet branch. The testnet branch files have been prepared for the Public Testnet environment. 

.. toctree::
    :maxdepth: 2

    testnets/public_testnet

  
-------------  
  
Private Testnets
-------------------------


If you want to test the feature set of current mainnet without connecting to mainnet, you must start a new (private) network. 

For a Private Testnet set up, you can download and use the testnet branch. However, you will have to set up necessary parameters' valuables for your Private Testnet **before** you start ``witness_node``. 


.. toctree::
    :maxdepth: 2
 
    testnets/private_testnet
  

-----------  
 
  
Other References 
------------------

* :ref:`how-to-setup-python-lib`

  - How to Create MPAs/UIAs with Python
  

* :ref:`Set up Faucet <how-to setup-faucet>`
* :ref:`Set up Nignx <how-to-setup-nignx>`
* :ref:`Code Coverage Testing (wiki) <how-to-testing-bts>`
* :ref:`How to verify block production <veryfy_block_production>`
* :ref:`How to backup a server <witness-backup-server>`
* :ref:`How witness Price Feeds work <witness-price-feeds>`

	
	
-------

Testnet Explorer
------------------

This tool allows you to check BitShares blockchain health. You can observe the BitShares blockchain operations/transactions, market volumes, assets, and fees. Also you can search "block", "Account", "Object", "Asset", and Transaction by the ID. 

* **Bitshares Blockchain Explorer for Testnet:** http://bitshares-testnet.xyz/ 
 
.. Note:: In the Testnet explorer, you may not find many activities. If you want to see the currest activities, pleaser check the Mainnet Explorer.
 
**APIs to query the Testnet** 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
 
The below API tools allow you to try BitShares APIs. You can set a value and click [Try it out!], then you will get a return. The return include a used Curl path format, Requested URL, and Response Body. Those information are very valuable to learn BitShares. 


* APIs - the Testnet Explorer uses to query the **Testnet**
  
  - http://148.251.10.231:5000/apidocs/ -> ES Wrapper : Wrapper to expose **Elasticsearch** Bitshares node data to  clients

On this page, we introduced a tool for the Testnet. We have the same tool for :ref:`the Mainnet of BitShares blockchain <explorer-wrappers>`.



-------------

* :ref:`consensus-changes-2018-06`

---------------------------

|

|