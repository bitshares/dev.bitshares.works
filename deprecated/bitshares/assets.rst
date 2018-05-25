

Assets/Tokens/Currencies
==========================

The BitShares 2.0 network consist of several *assets*, *tokens* or *currencies*.
All assets are equal from a technological point of view and come with more or
less the same features, namely, they can be traded against each other and can be
transfered within seconds. The differences between them are of economical
nature.

First, the reader should familiarize himself with the following
articles:

* Frequently Asked Questions & Guide: :ref:`assets-guide`



.. _uia:

User Issued Assets (UIAs)
---------------------------------

BitShares allows individuals and companies to create and issue their own
tokens for anything they can imagine. The potential use cases for so
called user-issued assets (UIA) are innumerable. On the one hand, UIAs
can be used as simple event tickets deposited on the customers mobile
phone to pass the entrance of a concert. On the other hand, they can be
used for crowd funding, ownership tracking or even to sell equity of a
company in form of stock.

All you need to do is click in order to create a new UIA is a few mouse
clicks, define your preferred parameters for your coin, such as supply,
precision, symbol, description and see your coin's birth after only a
few seconds. From that point on, you can issue some of your coins to
whomever you want, sell them and see them instantly **traded against any
other existing coin** on BitShares.

Unless you want some restriction. As the issuer, you have certain
privileges over your coin, for instance, you can allow trading only in
certain market pairs and define who actually is allowed to hold your
coin by using white- and blacklists. Of course, an issuer can opt-out of
his privileges indefinitely for the sake of trust and reputation.

As the owner of that coin, you don't need to take care of all the
technical details of blockchain technology, such as distributed
consensus algorithms, blockchain development or integration. You don't
even need to run any mining equipment or servers, at all.

So what's the drawback?

There is a drawback in this scenario, namely, a centralized issuance of
new tokens. To some extend, this could be managed by a hierarchical
multi-signature issuer account that prevents any single entity from
issuing new coins but instead requires a consensus among an arbitrary
set of people to agree on any changes to the coin.

Obviously, the regulations that apply to each kind of token vary widely
and are often different in every jurisdiction. Hence, BitShares comes
with tools that allow issuers to remain compliant with all applicable
regulations when issuing assets assuming regulators allow such assets in
the first place.

Use Cases
^^^^^^^^^^^^^^^^^

* Reward Points
* Fan Credits
* Flight Miles
* Event Tickets
* Digital Property
* Crowd-Funding
* Company Shares






.. _mpa:

Market Pegged Assets (MPAs)
------------------------------

A crypto-currency, with the properties and advantages of Bitcoin, that
is capable of maintaining price parity with a globally adopted currency
(e.g. U.S.  dollar), has high utility for convenient and censorship
resistant commerce. This can be achieved by BitShares' market pegged
assets (MPA), a new type of freely traded digital asset whose value is
meant to track the value of a conventional underlying asset by means of
an over-collateralized, counterpartyrisk-free, smart-contract secured 
blockchain loan.

Instead of creating a UIA where the full control over supply is in the
hands of the issuer, we can also create a **Market Pegged Asset** (MPA)
and let the market deal with demand and supply. All we need is a *fair
price* and another asset that can be used as collateral.

Why would we need *collateral* for? Since the issuer of a MPA has no
control over the supply, the blockchain protocol deals with increasing
and decreasing supply. In order for a user to get some of the new coins,
he will need to put collateral into a **smart contract** (technically,
this contract is a *collateralized loan*).

 A simple example would be a MPA that is backed by USD (a stable crypto
 token within BitShares) that requires a collateral ratio of 200%. Then,
 in order to get new coin, we can borrow 100 USD worth of new coins by paying
 200 USD.

By this, the supply of your coin is increased by 100. But how would it
be decreased? The USD are locked in the smart contract and can only be
reclaimed if the debt (here, 100 coins) are returned. Returning them
will result in the coins being removed from the supply because the are
no longer backed by any collateral.

So what for do we need a *fair price*? Remember that we chose a
collateral ratio of 200%? That number tells us how well *backed* your
coins are by the collateral. But what would happen if the value of your
coin goes to the moon?  Then your collateral ratio will reduce to say
150%. At a certain percentage, the blockchain will automatically trigger
so called *Margin Calls* which will

1. Take your collateral (here, USD)
2. Sell it in the market to buy back the coin you owe
3. Close the contract
4. Pay your the residual USD

A *fair price* thus tells the market what your coin is worth (e.g.
traded for on external exchanges) and triggers margin calls if
necessary.

But there is more! Everyone that holds your (MPA) coin in BitShares can
convert the coin into the backing asset at a fair price. This procedure
is called "settlement" and ensures that your MPA is always worth **at
least** the *fair price*.

In the User Interface, MPAs are easily distinguishable from UIAs in the
asset explorer.

SmartCoins
^^^^^^^^^^^^^^^^^^^^^^^^^^

BitAssets can be created and owned by anyone on the network. However,
those that are owned by the BitShares Committee, are called
*SmartCoins*. Among these are:

* (Bit)USD
* (Bit)CNY
* (Bit)EUR
* (Bit)GOLD
* (Bit)Silver

Balances in these assets are labeled with `USD`, `CNY`, etc., because
represent the same value as their underly.

Collateralized Tokens
^^^^^^^^^^^^^^^^^^^^^^^^^^

A *SmartCoin* (synonym for MPA) is a crypto-currency that *always* has 100% or
more of its value backed by the BitShares core currency (BTS), to which they can
be converted at any time, as *collateral* in a collateralized loan.

What makes MPAs unique is that they are free from counterparty risk even though
they resemble a collateralized loan. This is achievable by letting the
network itself (implemented as a software protocol) be responsible for securing
the collateral and performing settlements as will be described in more detail
below.

Market Mechanics
^^^^^^^^^^^^^^^^^^^^^^^^^^

Each BitAsset has a feed that is provided by the witnesses that indicate
a fair price for that asset. This so called *Settlement Price* or *Feed
Price* is used to margin call positions that borrowed BitAssets and can no
longer maintain the minimum collateral ratio (i.e. maintenance
collateral ratio). The collateral of these positions is used to buy back
the debt from the market automatically and the position will be closed.
By these rules, the network enforces the exchange participants to always
maintain a collateral that is higher than the minimum requirement.
Currently, the minimum required collateral ratio is **175%** and can be
changed by the witnesses.

Read more about the :doc:`margin call mechanics <dex-margin-mechanics>`
before trading.




.. _eba:

Exchange Backed Assets (EBA)
------------------------------

Exchange Backed Assets represent deposit receipts that are issued by a
centralized entity, such as exchanges, banks or other institutes. These
can either be interpreted as *I owe you* (IOUs) or certificates for a
deposit at that institute.

From the blockchain perspective, EBA are equivalent to a :ref:`uia` that
is created and issued by an exchange, bank or financial institute.
Hence, it is their responsibility to credit you with the corresponding
blockchain token (the EBA) on deposits.

Use Case
^^^^^^^^^^^^

The most common use case would be a centralized exchange that allows
their users to deposit crypto currencies in their wallets. These
deposits are usually stored in their own database and the customers
internal account balance is matched accordingly. These database balances
serve as **deposit receipts** but obviously require some trust that the
database is properly secured against any kind of attacks.

Instead of increasing an internal account balance of a user, new shares
of an EBA can be issued to the user on deposits. Since EBAs are
blockchain tokens, they can be traded on the decentralized exchange
similar to any other exchange.

In order to reclaim his crypto tokens on their native blockchain, the
users sends back the EBAs to the institute who then destroy the EBAs and
transfer the corresponding asset back to its rightful owner.

----------

Since EBAs are implemented as UIA, you can read more about the
underlying technology on the :ref:`corresponding page - UIA <uia>`.




.. _privbta:

Privatized BitAssets
-------------------------

Alternatively to regular MPA like the bitUSD, BitShares also offers
entrepreneurs an opportunity to create their own SmartCoins with custom
parameters and a distinct set of price feed producers.

Privatized SmartCoin managers can experiment with different parameters such as
collateral requirements, price feeds, force settlement delays and forced
settlement fees. They also earn the trading fees from transactions the issued
asset is involved in, and therefore have a financial incentive to market and
promote it on the network. The entrepreneur who can discover and market the best
set of parameters can earn a significant profit.  The set of parameters that can
be tweaked by entrepreneurs is broad enough that SmartCoins can be used to
implement a fully functional prediction market with a guaranteed global
settlement at a fair price, and no forced settlement before the resolution date.

Some entrepreneurs may want to experiment with SmartCoins that always trade at
exactly $1.00 rather than strictly more than $1.00. They can do this by
manipulating the forced settlement fee continuously such that the average
trading price stays at about $1.00. By default, BitShares prefers fees set by
the market, and thus opts to let the price float above $1.00, rather than fixing
the price by directly manipulating the forced settlement fee.



Parameters
~~~~~~~~~~~~

The relevant and interesting parameters are located in the uia flags:

.. code-block:: js

   {
      "witness_fed_asset" : false
      "committee_fed_asset" : false
   }

Setting these two parameters to ``false``, allows to manually define the
set of feed producers (see below). Alternatively, setting either of both
to ``true`` will give the corresponding entity the responsibility to
produce and publish a feed.

Changing the Feed producers
^^^^^^^^^^^^^^^^

The following command replaces the set of currently allowed feed
producers by the new set of feed producers:

::

    update_asset_feed_producers <symbol> ["prod-a", "prod-b"] True

Producing a Feed
^^^^^^^^^^^^^^^^

We have a distinct tutorial that describes how feed are can be
published: :ref:`publish-feed` .
 



.. _fba:

Fee Backed Asset
----------------------

Existing core features of the BitShares protocol are Market Pegged Assets (MPA)
and issuer backed User Issued Assets (UIA). In this proposal, we introduce
another type of asset: *Fee Backed Assets (FBA)*.

Feed backed assets allow to propose and fund *market based* innovation by
sharing a cut of future profits generated by this particular innovation with the
people that helped fund it. Think of it as a *Kickstarter* for features.
Hence, if people can profit from successful features in the form of fees then it
can help the BitShares ecosystem to become more adaptable over time as it
promotes innovation and can pay for its development.

If you have any features in mind that require new kind of transaction on the
blockchain, you can code that feature and fund it with an FBA.

Feed Backed Assets have been proposed in `BSIP-0007`_.

.. _BSIP-0007: https://github.com/bitshares/bsips/blob/master/bsip-0007.md





.. _pm:

Prediction Markets
--------------------------

   
A prediction market is a specialized BitAsset such that total debt and
total collateral are always equal amounts (although asset IDs differ).
No margin calls or force settlements may be performed on a prediction
market asset. A prediction market is globally settled by the issuer
after the event being predicted resolves, thus a prediction market must
always have the `global_settle` permission enabled. The maximum price for
global settlement or short sale of a prediction market asset is 1-to-1.

.. note:: In the following, we denote a *positive outcome* as a
          predication market that resolves to `true` (i.e. a price feed
          of `1`) and a *negative outcome* to resolve to `false` (i.e. a
          price feed of `0`)

If the bet resolves to `true` (i.e. a price feed of `1`), then the
PM-asset can be settled release the collateral to the holder of the
asset.

If, instead, the bet resolves to `false` (i.e. a price feed of `0`),
then those that sold the PM-asset on the market and went short, made a
profit since it PM-asset became worthless.

Creation
^^^^^^^^^^^^^^

Prediction markets are assets that trade freely and can be borrowed from
the market at a 1:1 ratio with the backing asset (which could be any
other asset, including BTS, USD, GOLD).

The technical details are described in a separate guide: :ref:`pm-create-manual`



Betting
^^^^^^^^^^^^

A user can take either bet on a positive outcome, or a negative outcome.
We here show how this works, technically.

Betting for a Positive Outcome
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are confident that the bet will resolve positive, you want to
**hold** that particular PM-asset since it allows you to settle it for
it's collateral on a 1:1 basis.

In order to get hold of those tokens, you can put a buy order for them
at any price (between 0 and 1) and wait for it to be filled, or buy at
market rates. By this technique, a user can pre define at which odds to
buy shares.

For instance, if you think that the bet resolves positively at a
probability of `80%`, you can put your buy order at a price of `0.8`. If
the bet resolves positively (price feed of `1`), then you can settle
your shares at `1` and make a 20% profit.

If you can buy tokens at a price of `0.2` (i.e. market participants
think it is unlikely to resolve positively), then you could make `80%`
profits at a risk of loosing with `80%` probability.

After closing of the bet, a user can claim his profits by **settling**
his borrow position and taking out the collateral:

* **Settlement in the CLI wallet**:
  ::

    >>> settle_asset <account> <amount> <symbol> True

* **Borrowing in the GUI wallet**:
  A settlement button is available when hovering the asset in your
  account's overview.

Betting for a Negative Outcome
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In order to bet for a negative outcome (bet resolves to `false` with a
price feed of `0`), you need to **sell** the tokens. In order to get
them, you should **not** buy them at the market, but instead **borrow**
them from the network by paying collateral at a 1:1 ratio.

For example, in the `PM.PRESIDENT2016` if you want to bet on a negative
outcome with `100k BTS`, you can borrow `100k PM.PRESIDENT2016` by paying
`100k BTS` to the network.

.. note:: Since PM-Assets can technically be pegged by any other asset,
          you may need to pay USD (or anything else) instead of BTS.

Once you borrowed the token, you can sell them at any price between `0`
and `1`. If you thing the probability of a negative outcome is `20%`,
you should consider selling your tokens at `0.2`.

If the bet resolves negatively (price feed of `0`), your debts is worth
`debt = amount * price = 0 BTS`, you can reclaim your collateral at zero
cost, and get to keep `20%` profits from selling the token at `0.2`. If
instead the bet resolves positively and you sold all tokens, you cannot
close your borrow position to redeem your collateral. However, your
total loss is reduced by `20%` for selling the tokens at the market.

If, by the end of the bet, you still have some of the tokens left, you
can of course close your borrow position partly and redeem the
corresponding percentage of the collateral.

* **Borrowing in the CLI wallet**:
  ::

     >>> borrow_asset <account> <amount> <PMsymbol> <1:1-amount> true

* **Borrowing in the GUI wallet**:
  Of course, the asset can also be borrowed in the **GUI/web wallet** by
  using the `Borrow x` button in the market.

  .. image:: pm-borrow-btn.png
          :alt: Borrow PM-Asset
          :width: 400px
          :align: center

Resolving
^^^^^^^^^^^^

A price feed needs to be published for the prediction market by the
issuer or feed producer. It is essentially a global settlement which
will set the parameters of the asset such that the holders of the asset
can settle at the outcome of the bet (0, or 1). The details are shown in
the guide:  :ref:`pm-close-manual`


|



