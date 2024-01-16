
******************************
Introduction & Architectures 
******************************


Welcome to BitShares! BitShares Blockchain implements an industrial-grade technology focused on businesses, organizations or individuals, with an amazing eco-system and free-market economy.  Though this documentation, we introduce BitShares Blockchain features and functions to create your Command-Line (i.e., CLI) wallet, application, or other programs for developers. 


.. contents:: Table of Contents
   :local:
   
-------



BitShares Members
===================

First, let's understand how we call BitShares Blockchain Community members and groups.  When you create a wallet and hold any BTS, you are a BTS Holder (i.e., shareholder). As a BTS Holder, you have a voting power to make decisions in the BitShares Blockchain Community. BTS Holders can vote for witnesses, committee members, and workers by using your wallet. As an option, if you wish,  you can case your voting power to a "Proxy" voter. The Proxy vote in behalf of BTS Holders account.  

.. image:: ../../_static/structures/btsholders-v1.png
        :alt: BitShares Architecture
        :width: 700px
        :align: center

----------------

BitShares Architecture 
==========================

GitHub Repositories and Entities
------------------------------------

This is an overview of BitShares Architecture entities. The purpose of this image is to bring the main elements together and share what types of resources you might find in the BitShares Github repositories. 

.. image:: ../../_static/structures/bitshares-architecture-v3notop.png
        :alt: BitShares Architecture
        :width: 650px
        :align: center

----------------

Key Design Concepts and the Features
=========================================

Key points and fundamentals for the design:
---------------------------------------------

- Keep everything in memory.
- Keep the core business logic in a single thread.
- Keep cryptographic operations (hashes and signatures) out of the core business logic.
- Divide validation into state-dependent and state-independent checks.
- Use an object-oriented data model.
- Avoid synchronization primitives (locks, atomic operations)
- Minimize unnecessary computation in the business logic processor.
    
BitShares is built to aim high-performance blockchain and has been done to remove all calculations that are not part of the critical, order-dependent, evaluation from the core business logic, and to design a protocol the facilitates these kinds of optimizations.


BitShares Available Features
--------------------------------

BitShares can be made to function as a software, a network, a ledger, a bank, an exchange, and a currency all at once. (e.g., It can fulfill the role of a bank by maintaining a distributed ledger that tracks debt collateralized by other assets. You can find out that BitShares offers numerous features that are not available on other popular blockchain platforms.

* **SmartCoins** are fungible, divisible and free from any restrictions. A SmartCoin is a cryptocurrency whose value is pegged to that of another asset, such as the US Dollar or gold. SmartCoins implement the concept of a collateralized loan and offer it on the blockchain.
* **Decentralized Exchange** - BitShares provides a high-performance decentralized exchange, with all the features you would expect in a trading platform. 

  - Secure: All of the reserves are kept as BTS held on the blockchain, and they cannot be stolen, because there are no private keys that can be compromised to steal the reserves.
   
* Trading / Financial Services 
* Transferable Named Account (human-friendly account name)
* Globally unique account name and ID.
* Dynamic Account Permissions
* Multi-user control for account
* Two authorities: owner and active keys
* Transaction + multi-signature authority
* **Proposed transaction infrastructure** 

  - witch tracks partially approved transactions.
  - It can be used for a scheduled payment 
   
* Fees calculation

  - Transaction fee
  - Fee Schedules 
   
* Assets - **User Issues Asset (UIA)** 

  - to help facilitate profitable business models for certain types of services.
  - *Use Cases* (Event tickets, Reward points, privatized SmartCoins, Predictions Market, more).
  - How to profit (i.e.,Fee pools)
	 
* BitAsset - bitUSD, bitEUR, bitCNY, and others.       
* **Delegated Proof of Stake Consensus (DPOS)** 
  
  - Under DPOS, BTS Holder has influence.
  - A robust and flexible consensus protocol.  
  
* Block Production by Elected witnesses
* **Referral Program** - to incentivize people to bring in more people.
* Vesting valance

----------------

BitShares Cash Flow
===================


.. image:: ../../_static/output/BitShares-Cashflow2.png
        :alt: BitShares Architecture
        :width: 700px
        :align: center

|
		
		
----------------

.. _trx-performance-explorer:

Blockchain Observation
===============================

Bitshares Block Explorer
-------------------------------

BitShares Explorer shows BitShares Blockchain information. You can observe BitShares Blockchain *Health* Status (head_block_num, head_block_age, chain_id, etc.), how transactions processing, assets volume, and members.

If you would like to see more detailed information, the Open Explorer offers other information tabs (i.e., Operations, Proxies, Markets, SmartCoins, UIAs, and Holders) to view.

- `Cryptofresh  <https://www.cryptofresh.com/>`_
- `BlockSights <https://blocksights.info/>`_
- `bts.ai <https://bts.ai/>`_


Blockchain Activity
---------------------

Blockchain Activity Matrix shows different tokens (assets) *Activity*, *Value*, and *Index*. It's interesting to check the CUI index is Capacity Utilization Index (a rate of blockchain daily activity to total blockchain capacity) to see how much numbers BitShares has.

- `Block'tivity <http://blocktivity.info/>`_
  

|

|

