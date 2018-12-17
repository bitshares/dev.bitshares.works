
Vesting Balances 
===================

In BitShares 2.0, some balances are vesting over time. This feature has been introduced initially in BitShares 1 when merging several blockchain businesses into one blockchain.



.. contents:: Table of Contents

-----------


What is it?
---------------------
Vesting balances contain any fees earned through the referral program or from worker pay, for example. They have a certain vesting period and are continually unlocked during that vesting period until all of the balances are available.
		
		
Account income
^^^^^^^^^^^^^^^^^^^

We make even more use of this functionality in such that an account's income in form of

* worker pay,
* witness pay,
* the referral program, or
* cashback

is vesting over several days with different strategies.

For instance, a worker can define for how long he would like his pay to vest to encourage BTS Holders to vote for him due to no imminent additional sell pressure from the worker.

**Strategies**

 1. **CCD / Coin Days Destroyed**

 The economic effect of this vesting policy is to require a certain amount of "interest" to accrue before the full balance may be withdrawn. Interest accrues as coindays (balance * length held). If some of the balance is withdrawn, the remaining balance must be held longer.

 2. **Linear Vesting with Cliff**

 This vesting balance type is used to mimic traditional stock vesting contracts where each day a certain amount vests until it is fully matured.

		
|
		
-------------

Claiming A Vesting Balance
-----------------------------

 You can claim vesting balances by using BitShares User interface. (You might need to wait certain days to claim.)

 Open the side dropdown menu and select **[Vesting Balances]**

.. image:: vesting2.png
        :alt: Vesting Balances
        :width: 200px
        :align: center
		
		
.. image:: vesting1.png
        :alt: Vesting Balances
        :width: 600px
        :align: center


		
|

|