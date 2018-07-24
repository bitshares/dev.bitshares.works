## Referral Program

The BitShares Blockchain has an integrated one-level referral system. Basically, everyone interacting on the BitShares blockchain needs to pay a *transaction fee*. From that fee (currently) 20% go into the Working Budget (for future funding of development etc.) and the other 80% go into the referral program from where, the registrar (who paid and assisted the registration process) as well as the referrer (who brought the user to the registrar) receive a cashback. 

To opt-out of the referral program, an account can be upgraded to a so called **Life-Time Member (LTM)** which replaces registrar and referrer for the original user to receive a 80% cashback on his fees

***

The purpose of the referral program is to incentivize people to bring in more people. It compares to a Multi-Level-Marketing (MLM) scheme with the difference of having only **1 level** where referred individuals can opt-out by upgrading their account to a [Life-Time Member (LTM)](/core/accounts#memberships-and-groups). Every life time member, can get a cut of the fees based on child accounts linked to ours via referral.

BitShares has several different kinds of accounts: 
* Basic Account, and
* Lifetime Member.

|   |   |
|---|---|
|Basic Accounts | Basic Accounts are free, but do not qualify for the referral program, nor any cash back on transaction fees. |
|Lifetime Members | LTMs pay an upgrade fee and earn **80% cash back on every fee they pay**. They also qualify for **80% of the fees paid by Basic Accounts they refer** to the network. These 80% can be split among the registrar, that actually registers the accounts, and an affiliate referrer, that brought in the new user. |

> **Note:** In Q1/2016, the *annual membership* has been removed from the code base and no longer exists. References to this kind of           memberships can be safely ignored.

The referral fees are controlled by the blockchain and are distributed like this:

* 20% go to the network
* 80% go to the referral program
  * of this 80%, x% go to the registrar
  * of this 80%, 100%-x% go to the affiliate referrer

> **Note:** For many cases it may make sense to upgrade the account even though you don't want to participate in marketing at all simply for the reasons to get a cashback of 80% of the fees you pay for your own transactions!

### What to do?

If you want to participate in the referral program, first you need to have a life-time member account. Then you can bring in new users by

* running your own faucet and actually register new accounts (will give you 80% of all the fees of those minus a fraction that you decide to give to affiliates (the referrers)
* referring people to a hosted wallet that offers you a cut of the fees as an affiliate.

  In this case, most hosted wallets add your account as affiliate if you provide the following link structure to people

      https://<url>/?r=<your-account>

  with ``<your-account>`` being the name of your BitShares Lite-Time Member account.

> **Note:** If you want to link to pages with in the wallet (i.e., a particular decentralized market), you need to have the ``?r=`` parameter **before** the ``#``, e.g.::

      https://<url>/?r=<your-account>#/market/USD_BTC


	 	  
			  
#### Examples

When an Basic Account pays $100 to become a Lifetime Member, $50 is paid to their Referrer, $30 is paid to the nearest Lifetime Member, and $20 is paid to the Network. After this point the Lifetime Member becomes its own referrer and nearest Lifetime Member and its prior Referrers no longer get any revenue from this user.

### Terms & Conditions

Please see the [Referral Program - Terms & Conditions](https://bitshares.org/referral-program-terms-and-conditions/) for
more details.

### Claiming Referral Bonus and Cashback

If you have a life time member account and 

* already paid some fees, or
* have referred people that paid some fees,

you can withdraw them from a [**Vesting balances**](/core/accounts#vesting-balances) page of your account.



***
