

Exchanges, Bridges, and Gateways
===================================

**Exchange**, **Bridges**, and **Gateways** represent business that trade or
exchange assets that are located **inside** a Graphene network (e.g. BitShares)
against assets that are **located** outside the blockchain network. For
instance, exchanges trade ``BTS:BTC`` while bridges exchange ``bitBTC:BTC``.

We here illustrate the steps necessary to securely operate as exchange or
gateway. Gateways take Fiat and convert them to their corresponding bitAsset at
a fee and vice versa. For instance:

1. A customer requests 100 bitUSD from a gateway
2. The gateway sends an invoice with bank account details
3. When the funds arrive at the gateway a percentage is taken as a fee and the
   rest is transfered as bitUSD directly into the BitShares wallet of the
   customer.

For exchanges we recommend to also read :ref:`what-is-different` and :ref:`often-used-calls`.

----------

**Integration Instructions**


.. include:: step-by-step.rst

----------

.. include:: supporting-features.rst

------------

.. include:: whitelist.rst

-------------

.. _securing-funds:

.. include:: securing-funds.rst


|



 
