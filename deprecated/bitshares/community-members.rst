
Community Members
===================

BitShares 2 separates responsibilities and incentives activities that are
beneficial to the network, thus acknowledging different skill sets and
interested community members to have incentives to contribute in the most
appropriate way.

* **Witnesses** are paid for maintaining the back-bone of the network.
* **Committee** members are unpaid volunteers that organize the community and propose changes to the network.
* **Marketers** are paid in referral fees.
* **Workers** are paid for whatever they propose and do.
* **Shareholders** are people holding BTS. They can cast a vote and influence the DAC's businesses

Each of the above (except Marketers) requires users to vote for the people,
proposals, and/or changes. Those with sufficient approval will be compensated.

Workers are the "catch all" group where if you have an idea for something that
could improve the network, you can get "paid" by the network to do it.
Organizing meet-ups, developing a new tool or feature for the community, and
maintaining websites and infrastructure (e.g. the mumble server team or linux
distribution) are all examples of things workers may do.

.. toctree::
    :maxdepth: 3

   shareholder
   committee
   witness
   worker

   
.. _community-shareholder:

Shareholder
-----------------

In contrast to most crypto-currencies, BitShares does not claim to be a
currency but rather an *equity* in a decentral autonomous company (DAC).
As a result, the market valuation of BitShares is free floating and may be as
volatile as any other equity (e.g. of traditional companies).

Every entity hold the core token (BTS) is considered a shareholder of the
BitShares decentralized company.

Nonetheless, BTS tokens can be used as *collateral* in financial smart contracts
such as market pegged assets and thus back every existing smartcoin such as the
bitUSD.

Furthermore, each BTS can be used to cast a vote for

* :doc:`Block Producers <witness>`
* :doc:`Commmittee members <committee>`

and can thus participate in the corporate governance.


Consensus Mechanism
^^^^^^^^^^^^^^^^^^^^^^^^^

Who exactly is allowed to produce a block at which time instant is defined by a consensus mechanism called Delegated Proof of Stake. In essence, you, the shareholders of BitShares can cast a vote for your preferred block producers on the blockchain. Those witnesses with the most votes are allowed to produce blocks.



.. _community-committee:

Committee
-------------

Since Bitcoin struggled to reach a consensus about the size of their blocks,
the people in the cryptocurrency space realized that the governance of a DAC
should not be ignored. Hence, BitShares offers a tools to reach on-chain
consensus about business management decisions.

The BitShares blockchain has a set of parameters available that are subject of
shareholder approval. Shareholders can define their preferred set of parameters
and thereby create a so called *committee member* or alternatively vote for an
existing committee member. The BitShares committee consists of several *active*
committee members.

The BitShares ecosystem has a set of parameters available that are subject of
shareholder approval. Initially, BitShares has the following blockchain
parameters:

* **fee structure**:         *fess that have to be paid by customers for individual transactions*
* **block interval**:        *i.e. block interval, max size of block/transaction*
* **expiration parameters**: *i.e. maximum expiration interval*
* **witness parameters**:    *i.e. maximum amount of witnesses (block producers)*
* **committee parameters**:  *i.e. maximum amount of committee members*
* **witness pay**:           *payment for each witnesses per signed block*
* **worker budget**:         *available budget available for budget items (e.g. development)*

Please note that the given set of parameters serves as an example and that the
network's parameters are subject to change over time.

Additionally to defining the parameters any active witness can propose a
protocol or business upgrade (i.e. hard fork) which can be voted on (or against)
by shareholders. When the total votes for the hard fork are greater than the
median witness weight `w` then the hard fork takes effect.


:ref:`committee-guide`


The committee is a set of entities that are approved by the shareholders and set policy for the Bitshares blockchain including:

* Transaction and trading fees
* Blockchain parameters, such as block size, block interval
* Referral and vesting parameters such as cash back percentage and vesting periods


.. _community-witness:

Witnesses
--------------------

Witnesses are entities that work for the blockchain by constructing new blocks. Their role is similar to the role of miners for Bitcoin and other blockchains. Each witness is approved by the shareholders and constructs and signs blocks from validated transactions. Every transaction made in the network is required to be validated by all witnesses.

In BitShares, the witnesses' job is to collect transactions, bundle them into a
block, sign the block and broadcast it to the network. They essentially are the
block producers for the underlying consensus mechanism.

For each successfully constructed block, a witness is payed in shares that are
taken from the limited reserves pool at a rate that is defined by the
shareholders by means of approval voting.

How to run a witness is described in a :ref:`witness-guide`



.. _community-worker:

Workers / Budget Items
--------------------------

Workers are proposals to perform a service in return for a salary from the blockchain. A worker proposal contains at a minimum the following information:

* A start and end date
* A daily pay
* A maximum total pay
* A link to a webpage where the worker proposal is explained

   
Worker Lifecycle
^^^^^^^^^^^^^^^^^^

Proposed
~~~~~~~~~~

These worker proposals have been submitted to the blockchain and are being actively voted on. In order to become active, they must exceed the refund400k worker in total votes.

Active
~~~~~~~~~

These worker proposals have exceeded the threshold and are being actively paid. Active workers can be defunded if their vote threshold is reduced below the refund400k worker level.

Expired
~~~~~~~~~~~~

These worker proposals are displayed for historical purposes. You will find propsals that have ended based upon their end date.

Worker budget mechanics
^^^^^^^^^^^^^^^^^^^^^^^^^

Workers receive pay from a fixed daily budget on a first-come, first-serve basis until there are no more funds left.

* A daily total budget of 400k BTS for all workers
* 5 worker proposals with a positive votes total, with daily pay requests of 100k BTS each

Now the four workers with the most votes will all receive 100k BTS each per day, but once they've been paid the worker budget is empty. Therefore the fifth worker will receive nothing.


------------

Thanks to the funds stored in the reserve pool, BitShares can offer to not only
pay for its own development and protocol improvement but also support and
encourage growth of an ecosystem.

.. contents:: Contents
   :local:
   
Payouts
^^^^^^^^^^^

A blockchain parameter (defined by shareholders through the committee) defines
the daily available budget. No more than that can be paid at any time to all
workers combined.

The daily budget is distributed as follows:

* The available budget is taken out of reserves pool.
* The budget items are sorted according to their approval rate (``Pro - Con``) in a descending order.
* Starting at the worker with the highest approval rate, the requested daily pay is payed until the daily budget is depleted.
* The worker with the least approval rate that was paid may receive less than the requested pay

Hence, in order to be successfully funded by the BitShares ecosystem, the
shareholder approval for your budget item needs to be highly ranked.

Since the payments for workers from the non-liquid reserve pool result in an
increased supply of BTS, these payments are vesting over a period of time
defined by shareholders.

Working for BitShares
^^^^^^^^^^^^^^^^^^^^^^

In order to be get paid by BitShares, a proposal containing

* the date of work begin,
* the date of work end,
* a daily pay (denoted in BTS),
* the worker's name, and
* an internet address.

has to be publish on the blockchain and approved by shareholders.

A worker can also choose on of the following properties:

* **vesting**: *pay to the worker's account*
* **refund**:  *return the pay back to the reserve pool to be used for future projects*
* **burn**:    *destroys the pay thus reducing share supply, equivalent to share buy-back of a company stock.*

A blockchain parameter (defined by shareholders through the committee) defines
the daily available budget. No more than that can be paid at any time to all so
called *workers* combined.

The daily budget is distributed follows:

* The available budget is taken out of reserves pool.
* The budget items are sorted according to their approval rate in a descending order.
* Starting at the worker with the highest approval rate, the requested daily pay is payed until the daily budget is depleted.
* The worker with the least approval rate that was paid may receive less than the requested pay

Hence, in order to be successfully funded by the BitShares ecosystem, the
shareholder approval for your budget item needs to be highly ranked.

Since the payments for workers from the non-liquid reserve pool result in an
increased supply of BTS, these payments are vesting over a period of time
defined by shareholders.

A description on how to create your own worker can be found in the
:doc:`tutorials <../tutorials/worker-create>`.

Pseudo Workers
^^^^^^^^^^^^^^^^^

Three types of pseudo workers exist that are not primarily used to for salary.

Polling Workers
^^^^^^^^^^^^^^^^^^^^

A worker proposal can be used to poll the shareholders for an opinion. Those
workers usually have no or very small pay. Additionally, they come with a
*proposal*, *recommendation* or other topic on which shareholders can publish a
binary opinion (pro, or contra).

Refund Worker
^^^^^^^^^^^^^^^^^^^^^

This worker is used to set an approval limit for worker proposals and their
payment by simply refunding his payment/salary to the reserve pool. If its 
amount of daily pay is as large as the daily available funds, and the worker
has highest approval among all worker proposals, all funds will be returned to
the reserves and no one will be payed. If, however, an other worker proposal has
more votes than the refund worker, the proposal gets paid its salary, and the
rest is return.

Burn Worker
^^^^^^^^^^^^^^

This type of worker is similar to the *Refund Worker* above but **burns** his
pay.


|






