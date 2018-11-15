
.. _bts-accounts:

********************
BitShares Accounts
********************

In BitShares, you can create an account relatively easily by using the BitShares UI wallet.  The account comes with the Private/public keys. You should keep the information safe. Also, BitShares account gives you some benefits (i.e., Lifetime Membership (LTM) and Referral Program) and important roles (i.e., voting).  We recommend you to read through BitShares Accounts information to learn more.

.. contents:: Table of Contents

-----------

|


Account
========================
In BitShares, you can create an account relatively easily by using the BitShares UI wallet. When you create a BitShares Account from the UI wallet, it will ask you to provide a unique Account name and a password. Although you should create a strong password, you can create readable and memorable BitShares account name. You do not need to write down a long random number as your account name. The BitShares account name would be helpful to identify other BitShares accounts.

It's important to understand that **only** you know your password, and no one can recover it.  The account information is registered to the blockchain with private/public keys of the account. Only the account creator knows the password and finds out the private keys.  In BitShares, there are three types of keys (i.e., active, owner, and memo key) which you can find them in the UI Wallet Permissions page.   

.. image:: ../../_static/structures/ui_create_account_keys.png
        :alt: UI Account create
        :width: 700px
        :align: center

		
BitShares UI Wallet
-----------------------

If you create an account at one of BitShares partners, the account name will be available among BitShares partners.  So, you will be able to use the BitShares account name to communicate (e.g., sending fund) with other BitShares account holders like sending an email. The advantage of using account name is you, and other people can identify the account holder. 

- Open `BitShare UI wallet <https://wallet.bitshares.org>`_

|

Identifier
-------------

When you create a BitShares account, BitShares 2.0 registers the account name to the blockchain and also assigns an incrementing identifier (account id) during the registration. One way to find your account id is to go to a `cryptofresh <https://www.cryptofresh.com/>`_ and search your account name. You will find your account id under your account name. 

The identifier comes with many advantages: Besides improved scalability, we have separated the identity from the transaction authorizing signature. In practice, owning an account name is autonomous from being able to spend its funds. Furthermore, both rights (we call them permissions) can split among an arbitrary complex relation of people (we call them authorities) using weights and required thresholds.

To separating authorities from identities, BitShares 2.0 can be much faster in processing delay while having much smaller transaction sizes. All participants are forced to have a named account on the blockchain. Also, most transactions are tied to the account name and can be linked to individuals (this includes transfers, trades, shorts, etc. but not stealthed transactions).

---------------

|

Memberships and Groups
=========================

Memberships
---------------

If you just created a new BitShares account, the account would be a **Basic member** account as a default.  

BitShares has created a **Lifetime member** (LTM) account to give the option to all BitShares holders.  The Lifetime Member get 80% cashback on Fees! If BitShares holders desire to profit from reduced fees and additional features, they might want to consider to buy Lifetime Subscription. 

To see your Membership stats on BitShares UI wallet, go to your account and open a side dropdown menu and select **Membership stats**.  You will find the current membership *upgrade fee* to become a LTM.


Membership Groups
------------------

.. list-table::
   :widths: 20 70
   :header-rows: 1

   * - group type 
     - note
   * - Non-Members  
     - (Basic member) A regular account is a non-member.   
   * - Lifetime Members 
     - Lifetime Members get a percentage cashback on every transaction fee they pay and income from referrals. A Lifetime membership is associated with a certain one-time fee defined in this schedule. Lifetime member (LTM) status can register an account. If you want to do so, upgrade the account.
   * - Annual Members
     - This membership has been removed. (see the below) 
	  
.. Note:: Technically, the fees that you pay stay the same, but a part of the fees is refunded in the form of a vesting balance. Once the fees have vested you can withdraw them. 

To see your vesting balances, open the side dropdown menu and select **Vesting balance**.  Vesting balances are recalculated hourly, so you might not yet see them right away.

.. WARNING:: In Q1/2016, the *annual membership* has been removed from the code base and no longer exists. References to this kind of memberships can be safely ignored.

.. note:: Due to some discrepancies, the annual membership has been disabled in most web wallets and will be re-enabled after a proper update eventually.

-------------

|

Fees
======

In the BitShares ecosystem every operation is assigned an individual fee. These fees are subject to change. However, they are defined solely by BTS Holder approval, thus each and every BTS Holder of the BitShares core asset (BTS) has a say as to what the fees should be. If BTS Holders can be convinced to reduce a certain fee and consensus is reached, the fee will be reduced automatically by the blockchain. Changes of blockchain parameters are proposed by members of the committee. These members are voted by BTS Holders and improve the flexibility and reaction rate.

Fee Division
----------------

Every time an account you referred pays a transaction fee, that fee is divided among several different accounts. The network takes a cut, and the Lifetime Member who referred the account gets a cut.

The registrar is the account that paid the transaction fee to register the account with the network. The registrar gets to decide how to divide the remaining fee between themselves and their affiliate.

Fees types
-----------

+---------------+----------------------------------------------------------------------------------------------------------+
|   fee  type   |  note                                                                                                    |
+===============+==========================================================================================================+
| Pending Fees  | Fees paid are only divided among the network, referrers, and registrars once every maintenance interval. |
+---------------+----------------------------------------------------------------------------------------------------------+                        
| Vesting Fees  | Most fees are made available immediately, but fees over the vesting threshold (such as those paid        |
|               | to upgrade your membership or register a premium account name) must vest for some period as defined      |
|               | by the committee.                                                                                        |                            
+---------------+----------------------------------------------------------------------------------------------------------+                        

---------------

|

.. include:: bts_permissions.rst 


|

.. include:: bts_multi-sign.rst 

	
.. include:: voting.rst  

---------------


.. include:: referral.rst  

---------------


.. include:: vesting_balances.rst  


---------------

|




		
		
		