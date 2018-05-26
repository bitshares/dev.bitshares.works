# Accounts

### Contents

- [Account](/source/accounts/accounts.md#account)
   - Memberships
- [Fees](/source/accounts/accounts.md#fees)
- [Permissions](/source/accounts/accounts.md#permissions)
- [Authorities](/source/accounts/accounts.md#authorities)
- Examples
   - [(Flat) Multi-Signature](/source/accounts/accounts.md#flat-multi-signature)
   - [(Flat) Flexible Multi-Signature](/source/accounts/accounts.md#flat-flexible-multi-signature)
   - [Multi-Hierarchical Flexible Multi-Signature](/source/accounts/accounts.md#multi-hierarchical-flexible-multi-signature)

******

BitShares account and permissions information. 


## Account

If you use BitShares UI (Wallet) to registrar an account, all you need to provide *an account name* and *a password*. 
your account name can be seen similar to a mail address in such that it is **unique** and every participant in the BitShares network can interact with you.

> Note: In contrast to any other platform you have ever used: Creating an account at one of our partners will make your account available at all the other partners as well.


### Membership

|   group type       |  note            |
| ------------------- |---------- |
| Non-Members    |  A regular account is a non-member. |                           
| Lifetime Members |Lifetime Members get a percentage cashback on every transaction fee they pay and qualify to earn referral income from users they register with or referer to the network. A Lifetime membership is associated with a certain one-time fee that is defined by the committee. *Lifetime member (LTM) status* can register an account. If you want to do so, upgrade the account. |                            
| Anual Members    |If a lifetime membership is too much you can still get the same cashback for the next year by becoming an annual subscriber for a smaller one-time fee which lasts for only one year. |         

> Note: Technically, the fees that you pay stay the same, but a part of the fees is refunded in the form of a vesting balance.Once the fees have vested you can withdraw them. To see your vesting balances, go to your “Account” tab, then click on “vesting balances” at the bottom left. Vesting balances are recalculated hourly, so you might not yet see them right away.

> Warning: Due to some discrepancies, the annual membership has been disabled in most web wallets and will be re-enabled after a proper update eventually.
                  
## Fees

Every time an account you referred pays a transaction fee, that fee is divided among several different accounts. The network takes a cut, and the Lifetime Member who referred the account gets a cut.

The registrar is the account that paid the transaction fee to register the account with the network. The registrar gets to decide how to divide the remaining fee between themselves and their own affiliate.

|   fee type       |  note            |
| ------------------- |---------- |
| Pending Fees    | Fees paid are only divided among the network, referrers, and registrars once every maintenance interval. |                           
| Vesting Fees | Most fees are made available immediately, but fees over the vesting threshold (such as those paid to upgrade your membership or register a premium account name) must vest for some days as defined by the committee.|                            

## Permissions

In BitShares, each account has defferent type of permissions. Both are implemented using [Elliptic Curve Cryptography](https://en.wikipedia.org/wiki/Elliptic_curve_cryptography) (ECC) with public and private keys.

|   permission type       |  note            |
| ------------------- |---------- |
| Owner Permission    | This permission has administrative powers over the whole account and should be considered for ‘backup’ strategies. |                           
| Active Permission | Allows to access funds and some account settings, but cannot change the owner permission and is thus considered the “online” permissions.|                            

> Note: You can find the keys for your permissions in your BitShsares UI wallet Settings.

## Authorities 

In BitShares an **authority** consists of one or many entities that authorize an action, such as transfers or trades.

An authority consists of one or several pairs of an account name with a **weight**.

In order to obtain a valid transaction, the sum of the weights from signing the parties has to exceed the threshold as defined in the permissions.

-----


## Examples  

Let’s discuss some examples to shed some light on the used terminology and the use-cases. We assume that a new account is created with it’s active permissions set as described below. Note that the same scheme also works for the owner permissions.

### (Flat) Multi-Signature

A flat multi-signature scheme is composed of `M` entities of which `N` entities must sign in order for the transaction to be valid. Now, in BitShares, we have **weights** and a **threshold** instead of `M` and `N`. Still we can achieve the very same thing with even more flexibility as we will see now.

Let’s assume, Alice, Bob, Charlie and Dennis have common funds. We want to be able to construct a valid transaction if only two of those agree. Hence a **2-of-4** (N-of-M) scheme can look as follows:

| Account | **Weight** |
|---------|--------|
| Alice  | 1  |
| Bob  |  1 |
| Charlie  |  1 |
| Dennis  |  1 |
| **Threshold:**  | 3  |

This means that each party has the same weight of 1 while 3 parties need to sign the transaction/proposal.

In other words: Alice, Bobe, Charlie and Dennis, each have 33% weight while 100% must be reached.

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/source/imgs/weight-threshold1.png" width="350" title="Multi">
</p>

All four participants have a weight of 33% but the threshold is set to 51%. Hence only two out of the four need to agree to validate the transaction.

Alternatively, to construct a 3-of-4 scheme, we can either decrease the weights to 17 or increase the threshold to 99%.

### (Flat) Flexible Multi-Signature

With the threshold and weights, we now have more flexibility over our funds, or more precisely, we have more **control**. For instance, we can have separate weights for different people. Let’s assume Alice wants to secure here funds against theft by a multi-signature scheme but she does not want to hand over too much control to her friends. Hence, we create an authority similar to:

| Account | **Weight** |
|---------|--------|
| Alice  | 49%  |
| Bob  | 25% |
| Charlie  | 25% |
| Dennis  | 10% |
| **Threshold:**  | 51%  |

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/source/imgs/weight-threshold2.png" width="350" title="Flexible">
</p>


Now the funds can either be accessed by Alice and a single friend or by all three friends together.

### Multi-Hierarchical Flexible Multi-Signature

Let’s take a look at a simple multi-hierarchical corporate account setup. We are looking at a company that has a Chief of Financial Officer (CFO) and a some departments working for him, such as the Treasurer, Controller, Tax Manager, Accounting, etc. The company also has a CEO that wants to have spending privileges. Hence we construct an authority for the funds according to:

| Account | **Weight** |
|---------|--------|
| CEO.COMPANY  | 51%  |
| CFO.COMPANY  | 51% |
| **Threshold:**  | 51%  |

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/source/imgs/weight-threshold3.png" width="350" title="Multi-Hierarchical">
</p>
    

whereas CEO.COMPANY and CFO.COMPANY have their own authorities. For instance, the CFO.COMPANY account could look like:     
  
| CFO.COMPANY | **Weight** |
|---------|--------|
| Chief.COMPANY  | 51%  |
| Treasurer.COMPANY  | 33% |
| Controller.COMPANY  | 33% |
| Tax Manager.COMPANY  | 10% |
| Accounting.COMPANY | 10% |
| **Threshold:**  | 51%  |

<p align="center">
  <img src="https://github.com/bitshares/how.bitshares.works/blob/master/source/imgs/weight-threshold4.png" width="750" title="Multi-Hierarchical">
</p>
    
This scheme allows:

- the CEO to spend funds
- the Chief of Finance Officer to spend funds
- Treasurer together with Controller to spend funds
- Controller or Treasurer together with wither the Tax Manager or Accounting to spend funds.

Hence, a try of arbitrary depth can be spanned in order to construct a flexible authority to reflect mostly any business use-case.


***

(ref)
- http://docs.bitshares.org/bitshares/user/account-permissions.html


