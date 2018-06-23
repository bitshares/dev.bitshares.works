## New Market API


From @svk31 on February 16, 2016 15:51

While these are a good start it's a shame none of the "all markets" methods were implemented. We desperately need a way to poll for active markets and obtain some stats about them. Approximate equivalents of these two would have been great:

	returnTicker: Returns the ticker for all markets
	return24Volume: Returns the 24-hour volume for all markets, plus totals for primary currencies


From @abitmore on February 16, 2016 15:57

@svk31 We simply have much much more markets than other exchanges, so a "all market" API is not practicable imo. However a "top X volume markets" API would be better.

From @svk31 on February 16, 2016 16:1

Yea that's why I said approximate equivalent. Even just the top 10 by volume would be amazing, but of course the more the better...


From @mvandeberg on February 16, 2016 17:7

@svk31 The market volume is being calculated every call. We are currently not saving that information in the database, so there is no quick way for sort on it and look up top 10 markets. That would require modification of how we store markets and orders and is beyond the scope of what I was asked to do.


From @mvandeberg on February 16, 2016 17:12

@abitmore I am not sure what specific differences you are referring to. Of course the APIs are different, they are different APIs. The point of the crypto API was to expose cryptographic utility methods through the network layer for utilization by the web client. All of the implementation is in [fc elliptic](https://github.com/cryptonomex/fc/blob/8eec508b8cd418f0719a58f10a11d7850e87b992/include/fc/crypto/elliptic.hpp). Unfortunately there is no documentation on those methods and I was only given a list of methods to expose. I assumed they were commonly understood methods to anyone familiar with elliptic curve cryptography.


From @abitmore on February 16, 2016 22:14

@pmconrad Please see https://github.com/cryptonomex/graphene/blob/fd09669be3ce017b038e341552552109595d3b0b/libraries/app/include/graphene/app/api.hpp#L199-L235 these are new added APIs without any document. Thanks for help.


From @pmconrad on February 17, 2016 19:4

blind_sign and unblind_signature belong to this: https://bitsharestalk.org/index.php/topic,17315.msg220421.html#msg220421
I suppose these two shouldn't even be there - AFAIK they're not used anywhere in graphene currently, and if they are the API should include the blind_hash function as well.

I don't know anything about the range stuff nor the blind_factor stuff.


***

(ref)
- Documentation of new market API #171
- Documentation of new market API #582
- es_objects plugin #500
- Add API to query for recent order histories of one account in one market #503
- https://github.com/bitshares/bitshares-core/issues/171
- Improve performance of `get_ticker` API #509 https://github.com/bitshares/bitshares-core/issues/509
- get_top_markets api #512
- add get_top_markets api call #560
- add get_top_markets #737
- Expand `get_top_markets` API to return more data fields #842





