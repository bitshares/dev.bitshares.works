## MPA can be backed by UIA (#56)

- [1] Can't create a smartcoin which is backed by an UIA #1537

***


Actually according to chain logic, MPA can be backed by UIA, except that the MPA is owned by `committee-account` , related code is [here](https://github.com/bitshares/bitshares-core/blob/2.0.180425/libraries/chain/asset_evaluator.cpp#L94-L110).


UIA = User Issued Asset
MPA = Market Pegged Asset
PM = Prediction Market

Smartcoins = MPA - produced by the committee
Bitasset = MPA, produced by users

Q. Does BitShares want to allow MPA1 to be backed by MPA2 to be backed by CORE? --> "YES"
 
PM is a specific variant of bitasset.
 
Q. If MPA2 wants to change its backing asset to something other than CORE, we need to check if there are any assets with MPA2 as a backing assets?  --> "YES"

... but the rulew also depend on the issuer of MPA1
- if MPA1 is committee-owned, then MPA2 must be backed by CORE.
- if MPA1 is User-owned, then MPA2 can be backed by UIA as well.
- In neither case, can MPA2 be backed by MPA3.

Q. Is there ANY case where the chain can be more than 3 assets? (e.g. MPA1 -> MPA2 -> MPA3 -> CORE) --> "NO"

-----

Given: "A" backed by "B" backed by "C", and "C" is a UIA. We need to check to make sure C cannot be changed to be backed by "A".

- "C" is an UIA, so, no need to check.
- If "C" is a UIA, it isn't backed by anything unable to change an UIA to MPA.

Q. What's UIA? 

- UIA means an asset which is not backed by another asset on chain.
- MPA means an asset backed by another

Q. So, an MPA can be issued by a non-committee, non-witness member, but is not considered UIA, as it is backed by something?

- MPA can be created but not issued.
- MPA (the asset object, not some quantity of that asset) can be "created" by anyone.
- "issue" means create some supply.

Q. What is the best way to determine?
  1. if an asset is User-issued?
  2. if an asset is committee-issued?
  
--> flag and issuer-permissions

----

- An UIA is a token whose units are explicitly created (aka issued) by its owner (aka issuer).
- A BitAsset cannot be issued but must be borrowed from the chain. So, an asset that has a price feed cannot be an UIA.

Q. So, only the assets that the price is determined by trading are UIA?

- An asset is user issued if asset_object.is_market_issued() returns `false`.
- An UIA doesn't have a feed price.
- MPAs have order book and trading and supply vs. demand.
- An UIA has a fixed price for "borrowing/settlement" but it has a variable trade price on the DEX just like any other token.
















