
.. _consensus-changes-2018-06:


BSIP38: Add target collateral ratio option to short positions
===================================================================

.. contents:: Table of Contents
   :local:
   
-------

Motivation
---------------------------------------------

From Abstract of `BSIP-38 <https://github.com/bitshares/bsips/blob/master/bsip-0038.md>`_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a short position is margin called, some of its collateral will be sold and some or all of its debt will be covered accordingly.

However, usually more collateral will be sold, in comparison to the minimum amount required to be sold to maintain the maintenance collateral ratio (MCR) requirement.

This BSIP proposes a protocol change to let shortes (borrowers) have control over selling how much collateral when being margin called.

This BSIP depends on `BSIP 31 <https://github.com/bitshares/bsips/blob/master/bsip-0031.md>`_ .

Discussion and Development
---------------------------------------------

* `Development Overview <https://github.com/bitshares/bitshares-core/issues/834>`_

* `Technical Discussion <https://github.com/bitshares/bitshares-core/pull/838>`_

* `Summary of Code Changes (Merge) <https://github.com/bitshares/bitshares-core/pull/838/files/610dbf519aa2a97523a6d753df93a00f368e4f8e>`_

* `Summary of Code Changes (Pull Request) <https://github.com/bitshares/bitshares-core/commit/14c9786ba9946599a6e13ccdb9928d9d152f2a7f>`_

* `Individual Code Changes <https://github.com/bitshares/bitshares-core/pull/838/commits>`_


Unit Tests
---------------------------------------------

* `Changes to the test framework that imitates a live blockchain (Implementation) <https://github.com/bitshares/bitshares-core/commit/14c9786ba9946599a6e13ccdb9928d9d152f2a7f#diff-472805f6e072745a1efe846e5a8d44f7>`_


The "database fixture" is a mock blockchain/database that imitates the internal database tracked by a node on the blockchain.

The modification permits the optional specification by accounts on the blockchain of a target collateral ratio on a short position (call order).

---------

* `Changes to test framework that imitates a live blockchain (Header) <https://github.com/bitshares/bitshares-core/commit/14c9786ba9946599a6e13ccdb9928d9d152f2a7f#diff-39d175fc7ddf571106cb34222239fb3b>`_


This header has been modified to publicize the optional use of a target collateral ratio for short position (call orders).

---------

* `Test the ability to create call orders with different target collateral ratios even without submitting them to the blockchain <https://github.com/bitshares/bitshares-core/commit/14c9786ba9946599a6e13ccdb9928d9d152f2a7f#diff-0f99622d84284fc682365f4b325b624e>`_


This functionality that is tested is used by client software (e.g. CLI, reference wallet, Android wallets) to describe an operation (an "intent") that will create or update a short position for an account on the blockchain. This operation should be internally consistent with itself and with the validity rules established for this operation. These intents are checked prior to submitting to an RPC node.

---------

* `Test of rounding of the multiplication of prices and quantities <https://github.com/bitshares/bitshares-core/commit/14c9786ba9946599a6e13ccdb9928d9d152f2a7f#diff-5984197c09fc34872620ff6206e29e35>`_


The functionality that is tested is used by the blockchain logic when filling orders.

--------

* `Test of call orders <https://github.com/bitshares/bitshares-core/commit/14c9786ba9946599a6e13ccdb9928d9d152f2a7f#diff-6f4426832d588f71164d10b4407f2137>`_


These tests are checking specific logic that occurs when investigating the status of existing short positions on the blockchain. The test investigates different short positions (call orders) given a particular feed price and a given match price (where matches are of a buy and sell order on the books). These different short positions have different collateral ratios some of which are not margin called, some of which need to be margin called yet have sufficient collateral, and some of which need to be margin called yet do not have sufficient collateral (i.e. black swan).

---------


* `Test of market orders <https://github.com/bitshares/bitshares-core/commit/14c9786ba9946599a6e13ccdb9928d9d152f2a7f#diff-5c2422e9af6788cd1cc3e76296e1c7c4>`_


Both tests are checking the matching of orders on the books.

The first test is checking the matching of a taker limit order with multiple maker call orders.

The second test is checking the matching of a maker limit order with multiple taker call orders.

---------

Contributor: @taconater

|

|