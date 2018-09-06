
******************************
Introduction & Architectures 
******************************


Welcome to BitShares! In this section, you will find the overview of BitShares Architecture (repositories and entities), and how the Core team processes the development work.  If you are interested in contributing to the Core team, you might want to check the Contribution Guide and the GitFlow to understand how BitShares-Core team handles the repository files and branches.  If you would like to know the Core team current project, issues, and the releases plans, check under the Issues and the Projects tabs.

.. contents:: Table of Contents
   :local:
   
-------



BitShares Members
===================

If you have an BitShares account, you are a BTS Holder and you have a voting power to make decisions in the BitShares Community.



.. image:: ../../_images/structures/btsholders-v1.png
        :alt: BitShares Architecture
        :width: 700px
        :align: center

----------------

BitShares Architecture 
==========================

GitHub Repositories and Entities
------------------------------------

 

.. image:: ../../_images/structures/bitshares-architecture-v3notop.png
        :alt: BitShares Architecture
        :width: 650px
        :align: center

----------------

Key Design Concepts and the Features
=========================================

BitShares is a Finance Smart contract platform supported by Graphene technology - Blockchain.  
BitShares is designed from the ground up to process more transactions every second than VISA and MasterCard combined.

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
   
* Assets
  - **User Issues Asset (UIA)** - to help facilitate profitable business models for certain types of services.
  - *Use Cases* (Event tickets, Reward points, privatized SmartCoins, Predictions Market, more)
  - How to profit? -> Fee pools	
	 
* BitAsset - BitUSD,       
* **Delegated Proof of State Consensus (DPOS)** - Under DPOS, BTS Holder has influence...
  - A robust and flexible consensus protocol.   
* Block Production by Elected witnesses
* Referral Program
* Vesting valance

----------------

Transactions and Performance
===============================

Bitshares Block Explorer
-------------------------------

BitShares Explorer shows BitShares Blockchain information. You can observe BitShares Blockchain *Health* Status (head_block_num, head_block_age, chain_id, etc.), how transactions processing, assets volume, and members.

If you would like to see more detailed information, the BitShares Open Explorer offers other information tabs (i.e., Operations, Proxies, Markets, SmartCoins, UIAs, and Holders) to view.

- `Cryptofresh  <https://www.cryptofresh.com/>`_
- `Open Explorer <http://open-explorer.io/>`_
- `bts.ai <https://bts.ai/>`_


Blockchain Activity
---------------------

Blockchain Activity Matrix shows different tokens (assets) *Activity*, *Value*, and *Index*. It's interesting to check the CUI index is Capacity Utilization Index (a rate of blockchain daily activity to total blockchain capacity) to see how much numbers BitShares has.

- `Block'tivity <http://blocktivity.info/>`_
  

|

|

