
*********************************
Using Whitelists and Blacklists
*********************************

.. contents:: Table of Contents
   :local:
   
--------------------


User Whitelists
=====================

Any live-time member can cast an opinion about other accounts using white- and
black-lists. They **do not** prevent anyone from interacting with your account
but serve as a basis for *list authorities*.

**Examples***

A user ``white`` can be added to the white-list of account ``provider`` with:::

    >>> whitelist_account provider white white_listed true

In contrast a ``black`` user can be added to its blacklist with:::

    >>> whitelist_account provider black black_listed true

Both can be removed from their lists with:::

    >>> whitelist_account provider black no_listing true
    >>> whitelist_account provider white no_listing true

**Definition**

White- and Black-listing of accounts works with the following API call:

.. doxygenfunction:: graphene::wallet::wallet_api::whitelist_account()

It expects a `new_listing_status` from

.. doxygenenum:: account_listing

-------------


Asset Market Whitelists 
========================

An issuer of an user-issued-asset may want to restrict trading partners for his
assets for legal reasons. For instance, a gateway for US dollar may not be
allowed to let his customers trade USD against CNY because additional licenses
would be required. Hence, in BitShares 2.0 we let issuers chose to restrict
trading partners with white- and black-lists.

**Example**

A gateway with IOU ``G.USD`` that wants to prevent his customers from trading
``G.USD`` against ``bitCNY`` can do so by adding ``bitCNY`` to the blacklist of
``G.USD`` by issuing:::

    >>> update_asset G.USD "" "{blacklist_markets:[CNY]}" true

Alternatively, if an issuer may want to only open the market ``G.USD : bitUSD``
with his asset, he can do so as well with:::

    >>> update_asset G.USD "" "{whitelist_markets:[USD]}" true

.. note:: The third argument for ``update_asset`` replaces the existing
   settings. Make sure to have all desired settings present.

**Definition**

Asset Market white-lists work with the following API call:

.. doxygenfunction:: graphene::wallet::wallet_api::update_asset

.. doxygenstruct:: graphene::chain::asset_options
     :members:

.. doxygenenum:: graphene::chain::asset_issuer_permission_flags


-----------------

.. _asset-user-whitelists:

Asset User Whitelists 
======================

Asset User white- and black-lists serve the need for companies to restrict
service to a subset of accounts. For instance, a fiat gateway may require
to follow KYC/AML regulations and can hence only deal with those
customers that have been verified accordingly. If the issuer of an user-issued
asset desires, he may set a restriction so that only users on the white-list
(and/or **not** on the blacklist) are allowed to hold his token.

Instead of putting all verified accounts into the respective asset's white-list
directly, BitShares 2.0 allows to define one or several white-list
*authorities*. In practice, the white- and black-lists of these accounts are
combined and serve as white- and black-lists for the asset.

This allows for easy out-sourcing of KYC/AML verification to 3rd-party
providers.

.. note:: By removing a user from the whitelist, funds can effectively be
   frozen.

**Example**

Let's assume user ``alice`` wants to own a gateways IOUs called ``G.USD`` which are
restricted by a whitelists. Before being able to own ``G.USD``, ``alice`` needs
to be white-listed by one of the authorities of ``G.USD``. 

Defining an asset's list authorities
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We now define the authorities (i.e. accounts) that define the white- and
blacklist of the asset ``G.USD``. We add ``g-issuer`` and ``kycprovider`` to
the white- and black-list:::

    >>> update_asset G.USD "" "{blacklist_authorities:[g-issuer, kycprovider], whitelist_authorities:[g-issuer, kycprovider], flags:white_list}" true

.. note:: The third argument for ``update_asset`` replaces the existing
   settings. Make sure to have all desired settings present.

Adding ``alice`` to a whitelist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's assume the only authority is the issuer ``g-issuer`` himself for
simplicity. The issuer now needs to add ``alice`` to ``g-issuer``'s account
whitelist:::

    >>> whitelist_account g-issuer alice white_listed true


**Definition**

White- and Black-listing of assets works with the following API call:

.. doxygenfunction:: graphene::wallet::wallet_api::update_asset

.. doxygenstruct:: graphene::chain::asset_options
     :members:

.. doxygenenum:: graphene::chain::asset_issuer_permission_flags


|

|

