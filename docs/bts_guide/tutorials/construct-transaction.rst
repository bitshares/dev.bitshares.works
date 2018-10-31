
.. _manually-construct-transaction:

Manually Construct Any Transaction
-------------------------------

General Procedure
^^^^^^^^^^^^^^^^^^^

The general principle for generating, singing and broadcasting an arbitrary transactions works as follows:

.. contents:: 
   :local:
   
------

1. Create an instance of the transaction builder
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The corresponding API calls in the :ref:`Transaction Builder <w-api-trans-builder>` are:

::

    >>> begin_builder_transaction
    >>> add_operation_to_builder_transaction $HANDLE [opId, {operation}]
    >>> set_fees_on_builder_transaction $HANDLE BTS
    >>> sign_builder_transaction $HANDLE true

The ``begin_builder_transaction`` call returns a number we call ``$HANDLE`` It allows to construct several transactions in parallel and identify them individually!


2. Add arbitrary operation types
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``opId`` and the JSON structure of the ``operation`` can be obtained with:

::

    get_prototype_operation <operation-type>

The operation types available are:

* :ref:`Operations List and the elements <lib-operations>`
* Learning BitShares-Core Available Operations! You can find Operations and OperationIDs pair `here. <https://github.com/abitmore/bitshares-core/blob/170523826b82ba754eeae8706a891797b4b37ee8/libraries/chain/include/graphene/chain/protocol/operations.hpp#L50>`_

3. Add the required amount of fees
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 
 
In practice, each operation has to pay a fee, and hence, each operation has to
carry a ``fee`` member. When crafting a transaction, you now have the choice
between either defining each fee for your operations individually, or you use
``set_fees_on_builder_transaction`` that sets the fee for each operation
automatically to the chosen asset.

3b. (Example) Transfer operation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A simple *transfer* takes the following form:

::

    get_prototype_operation transfer_operation
    [
      0,{
        "fee": {
          "amount": 0,
          "asset_id": "1.3.0"
        },
        "from": "1.2.0",
        "to": "1.2.0",
        "amount": {
          "amount": 0,
          "asset_id": "1.3.0"
        },
        "extensions": []
      }
    ]

The operation id for the ``transfer_operation`` is thus ``0`` (third line) and
the core elements (removing fee) of this operation take the form:

.. code-block:: js

      {
        "from": "1.2.0",
        "to": "1.2.0",
        "amount": {
          "amount": 0,
          "asset_id": "1.3.0"
        }
      }

We add an operation to a transaction as follows (line breaks inserted for
readability):

::

    >>> begin_builder_transaction
    0
    >>> add_operation_to_builder_transaction
         0
        [0,{
               "from": "1.2.0",
               "to": "1.2.0",
               "amount": {
                 "amount": 0,
                 "asset_id": "1.3.0"
               }
           }]

The corresponding ``id`` can be obtained with ``get_account``, and
``get_asset``.

4. Sign and broadcast your transaction and fees
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We add a fee payed in BTS, sign and broadcast the transaction (if valid):

::

    >>> set_fees_on_builder_transaction 0 BTS
    >>> sign_builder_transaction 0 true
		
|

--------------------
