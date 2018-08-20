## BitShares Python API Call - examples

#### Table of Contents:
-
-
-
-

***

### API

#### /account

~~~
curl -X GET --header 'Accept: application/json' 'http://185.208.208.184:5000/account?account_id=1.2.4'
~~~

Response Body

~~~
[
  {
    "active": {
      "account_auths": [],
      "address_auths": [],
      "key_auths": [],
      "weight_threshold": 0
    },
    "active_special_authority": [
      0,
      {}
    ],
    "blacklisted_accounts": [],
    "blacklisting_accounts": [],
    "id": "1.2.4",
    "lifetime_referrer": "1.2.4",
    "lifetime_referrer_fee_percentage": 8000,
    "membership_expiration_date": "1969-12-31T23:59:59",
    "name": "temp-account",
    "network_fee_percentage": 2000,
    "options": {
      "extensions": [],
      "memo_key": "BTS1111111111111111111111111111111114T1Anm",
      "num_committee": 0,
      "num_witness": 0,
      "votes": [],
      "voting_account": "1.2.5"
    },
    "owner": {
      "account_auths": [],
      "address_auths": [],
      "key_auths": [],
      "weight_threshold": 0
    },
    "owner_special_authority": [
      0,
      {}
    ],
    "referrer": "1.2.4",
    "referrer_rewards_percentage": 0,
    "registrar": "1.2.4",
    "statistics": "2.6.4",
    "top_n_control_flags": 0,
    "whitelisted_accounts": [],
    "whitelisting_accounts": []
  }
]
~~~

#### /account_id

~~~
curl -X GET --header 'Accept: application/json' 'http://185.208.208.184:5000/account_id?account_name=committee-account'
~~~

Response Body

~~~
"1.2.0"
~~~

Response Headers

~~~
{
  "access-control-allow-origin": "*",
  "connection": "keep-alive",
  "content-length": "8",
  "content-type": "application/json",
  "date": "Sat, 18 Aug 2018 04:29:04 GMT",
  "server": "nginx/1.6.2"
}
~~~

#### /acount_name

~~~
curl -X GET --header 'Accept: application/json' 'http://185.208.208.184:5000/account_name?account_id=1.2.0'
~~~

Response Body

~~~
"committee-account"
~~~

#### /accounts
(*Get an account list of the most 100 rich CORE holders.*)

~~~
curl -X GET --header 'Accept: application/json' 'http://185.208.208.184:5000/accounts'
~~~

Response Body

~~~
[
  {
    "account_id": "1.2.20067",
    "amount": "19019320146385",
    "name": "poloniexwallet"
  },
  {
    "account_id": "1.2.809079",
    "amount": "16119240552757",
    "name": "bitkkchubei002"
  },
  {
    "account_id": "1.2.479516",
    "amount": "11478766398323",
    "name": "binance-cold-1"
  },
  ...
 ]
~~~  
  

#### /block_header
  
#### /fees

#### /full_account

#### /get_asset

#### /get_block

#### /get_margin_positions
(*Get margin positions of an account.*)

~~~
curl -X GET --header 'Accept: application/json' 'http://185.208.208.184:5000/get_margin_positions?account_id=1.2.0'
~~~

Response Body

~~~
[
  {
    "borrower": "1.2.0",
    "call_price": {
      "base": {
        "amount": 34225280,
        "asset_id": "1.3.0"
      },
      "quote": {
        "amount": 389269979,
        "asset_id": "1.3.102"
      }
    },
    "collateral": 8556320,
    "debt": 55609997,
    "id": "1.8.15079"
  },
  {
    "borrower": "1.2.0",
    "call_price": {
      "base": {
        "amount": 1899819,
        "asset_id": "1.3.0"
      },
      "quote": {
        "amount": 4375,
        "asset_id": "1.3.111"
      }
    },
    "collateral": 7599276,
    "debt": 10000,
    "id": "1.8.15070"
  },
  {
    "borrower": "1.2.0",
    "call_price": {
      "base": {
        "amount": 2904123,
        "asset_id": "1.3.0"
      },
      "quote": {
        "amount": 43750,
        "asset_id": "1.3.112"
      }
    },
    "collateral": 2904123,
    "debt": 25000,
    "id": "1.8.15067"
  }
]
~~~


#### /get_market

#### /get_object

#### /header
(*Get some blockchain information.*)

~~~
curl -X GET --header 'Accept: application/json' 'http://185.208.208.184:5000/header'
~~~

Response Body

~~~
{
  "accounts_registered_this_interval": 19,
  "bts_market_cap": 2652005,
  "commitee_count": 11,
  "current_aslot": 29867340,
  "current_witness": "1.6.22",
  "dynamic_flags": 0,
  "head_block_id": "01c55c576a45d2827a87aeb70a5413074d45fad3",
  "head_block_number": 29711447,
  "id": "2.1.0",
  "last_budget_time": "2018-08-18T04:00:00",
  "last_irreversible_block_num": 29711430,
  "next_maintenance_time": "2018-08-18T05:00:00",
  "quote_volume": "31.24402381",
  "recent_slots_filled": "340282366920938463463374607431768211455",
  "recently_missed_count": 0,
  "time": "2018-08-18T04:48:18",
  "witness_budget": 23700000,
  "witness_count": 25
}
~~~




#### /last_network_transaction


#### /lookup_account

#### /lookup_asset

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /

#### /
#### /

#### /

#### /

#### /

#### /

