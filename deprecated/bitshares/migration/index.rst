
Migrating from BitShares 1.0 to BitShares 2.0
====================================================

This migration tutorial is relevant only to those customers and investors that
have participated in BitShares 1.0. We show improvements, new features and give
assistance for claiming your funds in BitShares 2.0.

-----------------

.. _what-is-new:

What is New in BitShares 2.0
-----------------------------


* **Votable Network Parameters**: 
  BitShares 2.0 will allow its shareholders to fine-tune any parameter
  available to the protocol. This includes, block size, block interval, but
  also the payment for block producers and transaction fees.

* **Flexible and Dynamic Access Control**:
  BitShares 2.0 allows customers and participants a flexible and dynamic
  access to its funds or account handle. A so called *Authority* can consist of
  a flat hierarchy similar to *multi-signature* in Bitcoin, but could also
  support tree hierarchies never to be seen before. Read more about this about
  `dynamic account permissions`_.

* **Transferable Account Names**:
  Since Control over Funds is separated from the control over an account, we
  can have transferable account names that are registered on the blockchain.  
  Named accounts allows for much easier transfers because no cryptic strings
  needs to be handed out. Read more about `transferable named accounts`_.

* **On-Chain Proposed Transactions**:
  In traditional crypto currencies, a multi-signature transaction has to be
  transfered to its corresponding signers on separated communication channels
  (off-chain). BitShares 2.0 allows to propose transactions on the chain and
  have the signers be notified for their required signature automatically. No
  more manual communications are required.

* **New Full-Node/Client Concept**:
  We recognize the hassles some people had when synchronizing the BitShares 1.0
  blockchain with the heavy-weighted BitShares full client. In order to offer
  more comfort and a faster trading experience, we decided to separated the
  user-interface from the block syncing core component that connects to the
  peer-to-peer network. Of course, both are open source and a full node can run
  easily, we understand that some users mainly prefer to use the frontend not
  bothering about the blockchain.

* **Referral Program**:
  PayPal and Dwolla showed the success of referral programs, a program that
  could easily and cheaper be implemented in a decentralized software protocol.
  Hence we took our chance and implemented a blockchain based referral program.
  From every transaction fee, paid by a customer you referred, you will get a
  fraction. Of course, this fraction can be tuned by shareholders! Read more
  about the `referral program`_.

* **Recurring & Scheduled Payments**:
  We wanted to offer a way to have our rent payed automatically. So we
  implemented it in the blockchain. In BitShares 2.0, participants are capable
  of allowing others to withdraw funds from your account. Of course, you can
  define a daily/weekly or monthly limit. Read more about `recurring and
  scheduled payments`_.

* **Additional Privatized BitAssets**:
  In contrast to Market Pegged Assets (also known as BitAssets) that have a
  price feed published by witnesses that have approval of shareholders, a
  *privatized* bitasset allows to create market pegged assets that have an
  individual set of price feed publishers that do not need shareholders'
  approval. Hence, everyone can create a privatized bitAsset to track an
  individual value, such as indices, or binary predictions.

.. _dynamic account permissions: https://bitshares.org/technology/dynamic-account-permissions/
.. _transferable named accounts: https://bitshares.org/technology/transferable-named-accounts/
.. _referral program: https://bitshares.org/referral-program/
.. _recurring and scheduled payments: https://bitshares.org/technology/recurring-and-scheduled-payments/

|

      
--------------
	  
.. _what-changed:

What has Changed since BitShares 0.9?
-----------------------------------------


* **BitAssets are Collateralized Loans from a Counterpartyrisk-Free Smart Contract**:
  Our research has identified an improved mechanism to achieve a solid *peg* of
  bitAssets to its underlay. BitAssets like the bitUSD in BitShares 2.0 will
  always trade for *at least* the value of its underlying asset, i.e. $1.
  We have summarized the economical analysis and incentives for market
  participants here: `bitAssets 2.0`_

* **Faster Blocks**:
  Initially, the BitShares 2.0 blockchain will come with 3 seconds block
  interval with the option to reduce down to 1 second if shareholders agree.

* **Industrial Performance**:
  BitShares 2.0 can support massive load and works well beyond 100k transactions
  per second. Find out how we achieve `industrial performance and scalability`_.

* **New Reactive UI**:
  The BitShares 1.0 user interface was powerful but lacking in responsiveness and perfomance. For Bitshares 2.0 we've reimplemented the whole wallet using the React.js framework developed by Facebook, which is well-known for having excellent performance. The new BitShares UI is an entirely browser-based wallet, with private keys maintained in the browser.
  We expect a flourishing ecosystem of forked and tweaked wallets based off of
  our UI.

* **Accounts must be registered**:
  In BitShares 2.0 we have separated *authorities* from transaction partners.
  Hence, if Alice wants to send funds to Bob, it may be required that only
  Celine signs for that transaction. Also, BitShares 2.0 has `a referral program`_.
  Both features combined make it necessary that participants *register* an
  account on the blockchain.

* **No more Hierarchies in Account Names**:
  In BitShares 1, there have been hierarchies in account names. Namely, you
  could only create a sub-account ``home.wallet`` if you also owned ``wallet``.
  In BitShares 2.0, these hierarchies no longer exist and to register
  ``home.wallet`` you don't need to own ``wallet``.

* **Explicit Privacy**:
  The *TITAN* technology in BitShares 2.0 slowed down blockchain processing
  significantly. Because of this and because TITAN did not really offer good
  privacy, we eliminated TITAN as a default transaction feature. 
  Hence: **Account transactions are public now as well.**
  However, since we recognize the value of financial privacy, we offer
  *blinded* transactions that hide the transferred *amount*, and *stealth*
  transactions that hide the sender and receiver. A combination of both is also
  possible.
 
* **Prices are Fractions**:
  To circumvent rounding errors, all prices in BitShares 2.0 are represented as
  fractions.

* **Delegates are now Witnesses and Payed Positions are now Budget Items**:
  Since we have separated the business part from the block producing part, we
  now call block producers (formerly known as *delegates*) witnesses, while the
  additional payed position for workers are called budget items.

.. _industrial performance and scalability: https://bitshares.org/technology/industrial-performance-and-scalability/
.. _bitAssets 2.0: https://bitshares.org/technology/price-stable-cryptocurrencies/
.. _a referral program: https://bitshares.org/technology/recurring-and-scheduled-payments/

|



----------


.. _blockchain-upgrade-2:

Blockchain Upgrade
--------------------

BitShares 2.0 will be initialized with what is called a *Genesis Block*. That
genesis block will be constructed from the balances of BitShares 1.0. BitShares
1.0 offers many features that need to be migrated into BitShares 2.0. To
simplify the process and reduce the risk of errors, the following conditions
will be met:

* **Funds**:

  * **BTS Tokens**: All BTS balances will be migrated 1:1. The supply not change!
  * **User-Issed-Assets**: All UAI tokens will be migrated 1:1
  * **BitAssets**: Because the new chain is a simple migration and should
    retain all the same "perceived value", all BitAssets and short positions are
    migrated 1:1.

* **Account Names**:

  Under BitShares 2.0, accounts are transferable and have different prices
  based upon the "quality" of the account name. Any "premium" names registered
  on or after 2015-06-08 (US Eastern time) will be given the prefix “bts-“ or
  similar after the migration. All account names registered on or after
  2015-06-18 (US Eastern time) will be prefixed with "bts-" unless they were
  registered using the BitShares Faucet. 

  * **Premium Name**:  No numbers and has vowles 
  * **Cheap Name**:    Has numbers or no vowels 

  All other account names will be migrated with their corresponding
  owner/active keys.

* **Open Orders**:
  Open orders (except open short positions) will **not** migrate and the funds
  will be credited to the corresponding owners.
* **Open Shorts**:
  Short orders will be migrated to BitShares 2.0 on a 1:1 ratio. You collateral
  will be imported as a separated account (e.g. ``usd-collateral-holder-124``)
  under your control.
* **Transaction History**:
  Transaction histories of BitShares 1.0 will be inaccessible in BitShares 2.0.
* **Vesting Balances**:
  Vesting balances will migrate under the existing terms, if two or more vesting
  balances were partially claimed as part of the same transaction prior to the
  snapshot the vesting balances may be merged into a single balance.
* **Unclaimed Delegate Pay**:
  Delegates that did not claim their pay prior to the snapshot will be able to
  claim their pay by importing their corresponding keys similar to any other
  balance.
* **Assets**:
  User issued assets and market pegged assets will migrated with their
  corresponding issuer and holders.
* **Deprecated Features**:
  Some features have turned out to be unreliable or impractical and will thus
  deprecate:

  * **Wall Messages** will not be migrated as the feature is now deprecated
  * Asset **description information** is no longer part of the blockchain state
    and will not be migrated
  * Account **public data** is deprecated and is no longer part of the blockchain state
  * BitShares URL scheme: `bts://` will be deprecated due to migration to hosted
    web wallets
	
|

   

--------------

|   