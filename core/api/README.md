## API

BitShares APIs are available by using [Remote Procedure Calls (RPC)](/core/api/rpc.md#remote-procedure-calls) and [WebSocket Calls & Notifications](/core/api/websocket_calls_notifications.md#websocket-calls-notifications).  All API calls are formatted in JSON and return JSON only.

There are [API Access Restrictions](/core/api/api_restrictions.md#api-access-and-restrictions). ( API 0 : state-less RPC-calls querying.  API 1: authenticated interaction by WebSocket.)  And you can restrict API's to particular users by setting the information into an [`api-access.json` ](/core/api/api_restrictions.md#accessing-restricted-apis).  You create the api-access.json file and specify a path to the api-access.json file in [`config.ini`](/core/nodes_full_witness/full_nodes.md#configuration) in the data directory. 

BitShares APIs can categorize two; [Blockchain API](https://bitshares.org/doxygen/namespacegraphene_1_1app.html) and [Wallet API](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html). Blockchain API is used to query blockchain data  (account, assets, trading history, etc).  And Wallet API is used to interact with the blockchain operations (create an account, sign, transfer, market orders, etc). The wallet has the private keys and signing capabilities, so, Wallet specific commands are only available if the wallet is connecting to cli_wallet and is unlocked. 

BitShares uses [Objects and IDs model](/core/api/object_ids.md#objects-and-ids) to represent the blockchain. The BitShares blockchains do not have the addresses that most cryptocurrency wallets have, instead each object is identified by a unique id, a type, and space. (i.e., BitShares Objects Format: space.type.id.  -- 1.1.x: base object, 1.2.x:account object, 1.3.x:asset object, etc)  

***

### [BitShares-Core API Documentation](https://bitshares.org/doxygen/hierarchy.html) - Doxygen Class Hierarchy

***


### BitShares APIs
- [API Access & Restrictions](/core/api/api_restrictions.md#api-access-and-restrictions)
   - [Using the API](/core/api/api_restrictions.md#using-the-api)
   - [Accessing Restricted API's ](/core/api/api_restrictions.md#accessing-restricted-apis)
   - [Accessing the Named API via HTTP](/core/api/api_restrictions.md#accessing-the-named-api-via-http)
   - [Login In](/core/api/api_restrictions.md#login-in)
- [Object and IDs](/core/api/object_ids.md#objects-and-ids)   
   
### Calls   
- [Remote Procedure Calls](/core/api/rpc.md#remote-procedure-calls)
- [Websocket Calls & Notifications](/core/api/websocket_calls_notifications.md#websocket-calls-notifications)

### FAQ - API
- [Go to FAQ list](/core/tutorials/FAQ.md#apis)


***
## Doxygen Documentation - API Group names 

### [Wallet API](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html)
- General Calls
- Wallet Calls
- Account Calls
- Trading Calls
- Asset Calls
- Governance
- Privacy Mode
- Blockchain Inspection
- Transaction Builder
      
### [Blockchain APIs](https://bitshares.org/doxygen/namespacegraphene_1_1app.html)
- [Database API](https://bitshares.org/doxygen/classgraphene_1_1app_1_1database__api.html)
- [Account History API](https://bitshares.org/doxygen/classgraphene_1_1app_1_1history__api.html)
- [Crypto API](https://bitshares.org/doxygen/classgraphene_1_1app_1_1crypto__api.html)
- [Network Broadcast API](https://bitshares.org/doxygen/classgraphene_1_1app_1_1network__broadcast__api.html)
- [Network node API](https://bitshares.org/doxygen/classgraphene_1_1app_1_1network__node__api.html)

      
### [Namespaces](https://bitshares.org/doxygen/namespacegraphene.html) 
- [Graphene::App](https://bitshares.org/doxygen/namespacegraphene_1_1app.html)
- [Graphene::Chain](https://bitshares.org/doxygen/namespacegraphene_1_1chain.html)
- [Graphene::Wallet](https://bitshares.org/doxygen/namespacegraphene_1_1wallet.html)

***
