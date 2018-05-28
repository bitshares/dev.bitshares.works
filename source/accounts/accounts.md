# Accounts

#### Contents:

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


***

(ref)
- http://docs.bitshares.org/bitshares/user/account-permissions.html


