
***********************
Getting Started 
***********************

.. _installation-guide:

Installation
========================

BitShares Core is the BitShares blockchain implementation and command-line interface. The web wallet is `BitShares UI <https://github.com/bitshares/bitshares-ui>`_.

Visit `BitShares.org <https://bitshares.org/>`_ to learn about BitShares and join the community at `BitSharesTalk.org <https://bitsharestalk.org/>`_.

**NOTE:** The official BitShares git repository location, default branch, and submodule remotes were recently changed. Existing repositories can be updated with the following steps::

	git remote set-url origin https://github.com/bitshares/bitshares-core.git
	git checkout master
	git remote set-head origin --auto
	git pull
	git submodule sync --recursive
	git submodule update --init --recursive
	
	
.. Attention:: See BitShares :ref:`system requirements <system-requirements-node>`.


Download and Build
-----------------------

Select an operation system and install and build.

.. toctree:: 
    :maxdepth: 1

    installation/build_ubuntu
    installation/build_osx
    installation/build_windows
    installation/build_windows_devenv
    installation/windows_cli_tool
	

Build and Run BitShares-Core in WSL (Installation Option)
---------------------------------------------------------

.. toctree:: 
    :maxdepth: 1

    installation/wsl
	
Known issues
-------------

.. toctree:: 
    :maxdepth: 1

    installation/boost_versions


------------------------

------------------------


Next Step Guide
========================

BitShares offers many features, after the bitshares-core installation, you might think what's the next step?  The below is a short contents guide that you might be interested to look into.

*If you want to...*
------------------------

Know more about how BitShares Works
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Check `How BitShares Works <http://how.bitshares.works/en/latest/index.html>`_ Guide
- Understand `BitShares Accounts <http://how.bitshares.works/en/latest/user_guide/accounts/bts_account.html>`_ Features
- Read about Types of `Asset Tokens <http://how.bitshares.works/en/latest/bts_holders/tokens.html>`_


Create a BitShares GUI wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Check `BitShares-UI Release <https://github.com/bitshares/bitshares-ui/releases>`_
- Read how to create `BitShares UI Wallet  <http://how.bitshares.works/en/latest/user_guide/create_account.html>`_ steps 
- Understand `BitShares Client and Login Mode <http://how.bitshares.works/en/latest/user_guide/bitshares-client.html>`_ 


Know how to Backup/Restore (GUI) 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
 
- Understand `How to Backup and Restore your wallet <http://how.bitshares.works/en/latest/user_guide/backup_local_wallet.html>`_ 


Create and launch a CLI wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
 
- Understand `CLI Client and Wallet -  Connectivity <https://dev.bitshares.works/en/master/development/apps/cli_intro.html>`_
- Try `Connecting a CLI wallet <https://dev.bitshares.works/en/master/development/apps/cli_wallet.html>`_  section 
- Learn how to setup :ref:`secure-network-configuration`

Run a node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Understand about :ref:`BitShares Node(s) <how-to-run-full-node>`
- Know the :ref:`Node configuration and System Requirements <system-requirements-node>`
- Learn :ref:`How to run and Use a Full Node <how-to-run-full-node2>`
- Learn :ref:`How to run a BitShares API Node <run-api-node-guide>`
- Check out :ref:`Node Guide <witness-node-guide-tutorials>`
- Try it in a :ref:`BitShares Public TestNet <public-testnet-details>`


Use BitShares API 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Learn :ref:`API Restrictions <api-access-and-restrictions>`
- Understand Bitshares :ref:`Objects and IDs and the format <objects-ids>`
- Understand BitShares :ref:`Remote Procedure Calls (RPC) <rpc>` 
- Understand BitShares :ref:`Websocket Calls & Notifications <websocket-calls>`
- Try :ref:`bis-explorer-api-tryitout`
- Look into BitShares APIs

  - :ref:`Blickchain API <blockchain-api>`
  - :ref:`Wallet API <wallet-api-calls>`

Learn BitShares system components
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Understand about :ref:`block(s) <lib-block>`
- Read BitShares' :ref:`Protocols <lib-protocols>`
- Read BitShares' :ref:`Operations <lib-operations>`
- Learn BitShares' :ref:`Objects and Elements <lib-objects>`

.. _bitshares-other-language-support:

Know BitShares other languages' support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Check :ref:`Python BitShares information links <lib-python>`
- Check  `BitSharesjs: JavaScript Bitshares library <https://bitsharesjs.bitshares.org/>`_
 


Know about BSIPs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Read about :ref:`BitShares Improvement Proposals and Protocols (BSIPs) <about-bsips>`
- Check the `BSIP Repository <https://github.com/bitshares/bsips>`_ 

  - In the repository, you will find submitted technical documents. They describe the process of updating and improving the BitShares blockchain and technical ecosystem.

|




	
	
	
	
	
	
	 
	
|

