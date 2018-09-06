
.. _claiming-vesting-balance:

Claiming A Vesting Balance
-------------------------

Console Wallet
^^^^^^^^^^^^^
From the CLI wallet, vesting balances from witnesses can be claimed by
using:::

    withdraw_vesting <account> <amount> <asset>

Unfortunately, no call exists for non-witness-pay vesting balances, yet
but a transaction can be :doc:`constructed manually <construct-transaction>`
with the operation ``vesting_balance_withdraw_operation`` and takes the
form:

.. code-block:: js

    [
      33,{
        "fee": {
          "amount": 0,
          "asset_id": "1.3.0"
        },
        "vesting_balance": "1.13.0",
        "owner": "1.2.0",
        "amount": {
          "amount": 0,
          "asset_id": "1.3.0"
        }
      }
    ]

		
|

--------------------
