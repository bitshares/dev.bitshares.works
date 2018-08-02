## API Access and Restrictions

#### Table of Contents:

- [Using the API](../api/api_restrictions.md#using-the-api)
  - Examples
- [Accessing Restricted API's ](../api/api_restrictions.md#accessing-restricted-apis)
   - Example 
- [Accessing the Named API via HTTP](../api/api_restrictions.md#accessing-the-named-api-via-http)
   - Example    
- [Login In](../api/api_restrictions.md#login-in)
   - *class* graphene::app::login_api
   
***

## Using the API

We provide several different API's. Each API has its own ID. When running `witness_node`, initially two API's are available.

- **API 0  provides read-only access to the database. (e.g. state-less RPC-calls querying)**
- **API 1  is used to login and gain access to additional, restricted API's. (e.g. authenticated interaction)**

> Note: To access the restricted API-1 we are required to use the websocket connection with callbacks to access API-1


#### Examples

#### using wscat package from npm for websockets:

      $ npm install -g wscat
      $ wscat -c ws://127.0.0.1:8090
      > {"id":1, "method":"call", "params":[0,"get_accounts",[["1.2.0"]]]}
      < {"id":1,"result":[{"id":"1.2.0","annotations":[],"membership_expiration_date":"1969-12-31T23:59:59","registrar":"1.2.0","referrer":"1.2.0","lifetime_referrer":"1.2.0","network_fee_percentage":2000,"lifetime_referrer_fee_percentage":8000,"referrer_rewards_percentage":0,"name":"committee-account","owner":{"weight_threshold":1,"account_auths":[],"key_auths":[],"address_auths":[]},"active":{"weight_threshold":6,"account_auths":[["1.2.5",1],["1.2.6",1],["1.2.7",1],["1.2.8",1],["1.2.9",1],["1.2.10",1],["1.2.11",1],["1.2.12",1],["1.2.13",1],["1.2.14",1]],"key_auths":[],"address_auths":[]},"options":{"memo_key":"GPH1111111111111111111111111111111114T1Anm","voting_account":"1.2.0","num_witness":0,"num_committee":0,"votes":[],"extensions":[]},"statistics":"2.7.0","whitelisting_accounts":[],"blacklisting_accounts":[]}]}

#### the same thing using an HTTP client such as curl for API's which do not require login or other session state:

      $ curl --data '{"jsonrpc": "2.0", "method": "call", "params": [0, "get_accounts", [["1.2.0"]]], "id": 1}' http://127.0.0.1:8090/rpc
      {"id":1,"result":[{"id":"1.2.0","annotations":[],"membership_expiration_date":"1969-12-31T23:59:59","registrar":"1.2.0","referrer":"1.2.0","lifetime_referrer":"1.2.0","network_fee_percentage":2000,"lifetime_referrer_fee_percentage":8000,"referrer_rewards_percentage":0,"name":"committee-account","owner":{"weight_threshold":1,"account_auths":[],"key_auths":[],"address_auths":[]},"active":{"weight_threshold":6,"account_auths":[["1.2.5",1],["1.2.6",1],["1.2.7",1],["1.2.8",1],["1.2.9",1],["1.2.10",1],["1.2.11",1],["1.2.12",1],["1.2.13",1],["1.2.14",1]],"key_auths":[],"address_auths":[]},"options":{"memo_key":"GPH1111111111111111111111111111111114T1Anm","voting_account":"1.2.0","num_witness":0,"num_committee":0,"votes":[],"extensions":[]},"statistics":"2.7.0","whitelisting_accounts":[],"blacklisting_accounts":[]}]}

#### API 0 is accessible using regular JSON-RPC:

      $ curl --data '{"jsonrpc": "2.0", "method": "get_accounts", "params": [["1.2.0"]], "id": 1}' http://127.0.0.1:8090/rpc

Since restricted APIs require login, they are **only** accessible over the websocket RPC. However, to monitor incoming deposits, we only need API 0.


***

## Accessing Restricted API's 

You can restrict API’s to particular users by specifying an `api-access` file in [`config.ini`](../nodes_full_witness/full_nodes.md#configuration) or by using the `--api-access /full/path/to/api-access.json` startup node command. 

#### Example: 
`api-access.json` file which allows user `bytemaster` with password `supersecret` to access *four different API’s*, while allowing any other user to access the *three public API’s* necessary to use the wallet::

    {
       "permission_map" :
       [
          [
             "bytemaster",
             {
                "password_hash_b64" : "9e9GF7ooXVb9k4BoSfNIPTelXeGOZ5DrgOYMj94elaY=",
                "password_salt_b64" : "INDdM6iCi/8=",
                "allowed_apis" : ["database_api", "network_broadcast_api", "history_api", "network_node_api"]
             }
          ],
          [
             "*",
             {
                "password_hash_b64" : "*",
                "password_salt_b64" : "*",
                "allowed_apis" : ["database_api", "network_broadcast_api", "history_api"]
             }
          ]
       ]
    }

- Passwords are stored in `base64` as as salted `sha256` hashes. 
- A simple Python script, `saltpass.py` is avaliable to obtain hash and salt values from a password. 
- A single asterisk `*` may be specified as username or password hash to accept any value.

With the above configuration, how to call `add_node` from the `network_node` API

    {"id":1, "method":"call", "params":[1,"login",["bytemaster", "supersecret"]]}
    {"id":2, "method":"call", "params":[1,"network_node",[]]}
    {"id":3, "method":"call", "params":[2,"add_node",["127.0.0.1:9090"]]}

> Note: the call to `network_node` is necessary to obtain the correct API identifier for the network API. It is not guaranteed that the network API identifier will always be `2`.

The full node offers a set of API(s), of which only the database calls are avaiable via RPC. Calls that are restricted by default (i.e. `network_node_api`) or have been restricted by configuration are not accessible via RPC because a statefull protocol (websocket) is required for login.

The [`network_node API`](https://bitshares.org/doxygen/classgraphene_1_1app_1_1network__node__api.html) requires login, it is only accessible over the websocket RPC. 

Please check. Our [doxygen](https://bitshares.org/doxygen/) documentation contains the most up-to-date information about APIs.

***

## Accessing the Named API via HTTP

When you access the API via HTTP, use the name of the APIs like below,

    curl --data '{"jsonrpc": "2.0", "params": ["history", "get_account_history", ["1.2.31489", "1.11.0", 10, "1.11.0"]], "method": "call", "id": 10}' http://testgraphene:8090/rpc

If the API is restricted, specify the credentials using standard HTTP Headers,

    curl -u bytemaster:supersecret --data '{"jsonrpc": "2.0", "params": ["history", "get_account_history", ["1.2.31489", "1.11.0", 10, "1.11.0"]], "method": "call", "id": 10}' http://testgraphene:8090/rpc

When you use WS, use like below to prevent unnecessary round trips (login, query API number, call function)

    wscat -c ws://bytemaster:supersecret@testgraphene:8090

***

## Login In

The `login_api` class implements the bottom layer of the RPC API. All other APIs must be requested from this API. 

#### [*class* `graphene::app::login_api`](https://bitshares.org/doxygen/classgraphene_1_1app_1_1login__api.html)

| -- | |
|------------------------|-----------------------------------------------------------------|
| Public Functions |  bool `login` (const string &user, const string &password) <br/>  Authenticate to the RPC server.  |
| Return |  True if logged in successfully; false otherwise |
| Note | This must be called prior to requesting other APIs. Other APIs may not be accessible until the client has sucessfully authenticated.  |
| Parameters | - `user`: Username to login with <br/> - `password`: Password to login with | 



| fc::api\<\>  | |
|------------------------|-----------------------------------------------------------------|
| fc::api<block_api> block() const | Retrieve the network block API.  |
| fc::api<network_broadcast_api> network_broadcast() const | Retrieve the network broadcast API.  |
| fc::api<database_api> database() const | Retrieve the database API. |
| fc::api<history_api> history() const | Retrieve the history API. |
| fc::api<network_node_api> network_node() const | Retrieve the network node API. |
| fc::api<crypto_api> crypto() const | Retrieve the cryptography API.  |
| fc::api<asset_api> asset() const | Retrieve the asset API. |
| fc::api<graphene::debug_witness::debug_api> debug() const | Retrieve the debug API (if available)  |
| void enable_api(const string &api_name) | Called to enable an API, not reflected. |


***

(ref)

- https://github.com/bitshares/bitshares-core/wiki/API
- http://docs.bitshares.org/api/access.html
- https://bitshares.org/doxygen/index.html
- https://github.com/bitshares/bitshares-core/issues/624
- https://github.com/bitshares/bitshares-core/pull/223


