# BitShares API

#### Table of Contents: 
- [BitShares-Core API References](../api/API_references.md#bitshares-core-api-references---doxygen-documentation-quick-links)
  - Blockchain APIs: [list](../api/API_references.md#bitshares-core-api-references---doxygen-documentation-quick-links) - [open doxygen](https://bitshares.org/doxygen/namespacegraphene_1_1app.html) 
  - Wallet API: [list](../api/API_references.md#bitshares-core-api-references---doxygen-documentation-quick-links) - [open doxygen](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html) 
- [API Access & Restrictions](../api/api_restrictions.md#api-access-and-restrictions)
- [Objects and IDs](../api/object_ids.md#objects-and-ids)  
  - [Objects Format](../api/object_ids.md#bitshares-objects-format)
  - [List of Commonly used Objects](../api/object_ids.md#list-of-commonly-used-objects)
    - [Examples](../api/object_ids.md#examples)
    - [Examples - Accounts](../api/object_ids.md#examples---accounts)
    - [Example Call and a result: account object (1.2.x)](../api/object_ids.md#example-call-and-a-result-account-object-id-12x)
    - [Example Call and a result: asset object (1.3.x) ](../api/object_ids.md#example-call-and-a-result-asset-object-id-13x)
  - [Defenitions](../api/object_ids.md#definitions)
    - [Protocol Space (1.x.x)](../api/object_ids.md#protocol-space-1xx)
    - [Implementattion Space (2.x.x)](../api/object_ids.md#implementattion-space-2xx)
- [Available Calls](../api/README.md#available-calls) 
  - [Remote Procedure Calls (RPC)](../api/rpc.md#remote-procedure-calls)
  - [Websocket Calls & Notifications](../api/websocket_calls_notifications.md#websocket-calls-notifications)
- [Often Used API Calls](../api/often_used_call.md#often-used-api-calls)
- [FAQ - API](../tutorials/FAQ.md#apis)

***

BitShares APIs are available by using [Remote Procedure Calls (RPC)](../api/rpc.md#remote-procedure-calls) and [WebSocket Calls & Notifications](../api/websocket_calls_notifications.md#websocket-calls-notifications).  All API calls are formatted in JSON and return JSON only.

There are [API Access Restrictions](../api/api_restrictions.md#api-access-and-restrictions). ( API 0 : state-less RPC-calls querying.  API 1: authenticated interaction by WebSocket.)  And you can restrict API's to particular users by setting the information into an [`api-access.json` ](../api/api_restrictions.md#accessing-restricted-apis).  You create the api-access.json file and specify a path to the api-access.json file in [`config.ini`](../nodes_full_witness/full_nodes.md#configuration) in the data directory. 

BitShares APIs can categorize two; [Blockchain API](https://bitshares.org/doxygen/namespacegraphene_1_1app.html) and [Wallet API](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html). Blockchain API is used to query blockchain data  (account, assets, trading history, etc).  And Wallet API is used to interact with the blockchain operations (create an account, sign, transfer, market orders, etc). The wallet has the private keys and signing capabilities, so, Wallet specific commands are only available if the wallet is connecting to cli_wallet and is unlocked. 

BitShares uses [Objects and IDs model](../api/object_ids.md#objects-and-ids) to represent the blockchain. The BitShares blockchains do not have the addresses that most cryptocurrency wallets have, instead each object is identified by a unique id, a type, and space. (i.e., BitShares Objects Format: space.type.id.  -- 1.1.x: base object, 1.2.x:account object, 1.3.x:asset object, etc)  

***

### [BitShares-Core API References](../api/API_references.md#bitshares-core-api-references---doxygen-documentation) - Doxygen 
Blockchain APIs, Wallet API Calls, Objecs IDs, and objects information
***

## API Access & Restrictions
   - [Using the API](../api/api_restrictions.md#using-the-api)
   - [Accessing Restricted API's ](../api/api_restrictions.md#accessing-restricted-apis)
   - [Accessing the Named API via HTTP](../api/api_restrictions.md#accessing-the-named-api-via-http)
   - [Login In](../api/api_restrictions.md#login-in)

## Objects and IDs
BitShares-Core uses a different model to represent the blockchain(space), its transactions(type) and accounts(unique ids). On the BitShares blockchains there are no addresses, but objects identified by a unique id, a type, and space.

**BitShares Objects Format**

      space.type.id

- [List of Commonly used Objects](../api/object_ids.md#objects-and-ids)   
BitShares objects were identified by a unique id, an type and a space. You should know some of them.
   
   
**Example Call and a result: account object (id: 1.2.x)**

		unlocked >>> get_account_id "user123"
		
		get_account_id "user123"
		"1.2.539269"
		unlocked >>>


**Example Call and a result: asset object (id: 1.3.x)**

		unlocked >>> list_assets "BTS" "2"
		
		list_assets "BTS" "2"
		[{
				"id": "1.3.0",
				"symbol": "BTS",
				"precision": 5,
				"issuer": "1.2.3",

			....
				"dynamic_asset_data_id": "2.3.368"
			}
		]		


## Available Calls   
- [Remote Procedure Calls (RPC)](../api/rpc.md#remote-procedure-calls)
- [Websocket Calls & Notifications](../api/websocket_calls_notifications.md#websocket-calls-notifications)



## FAQ - [API](../tutorials/FAQ.md#apis)
 - [How an Exchange can integrate their services to the BitShares UI Wallet - Gateway Integration Requirements](https://github.com/bitshares/bitshares-ui/wiki/Gateway-Integration-Requirements) : from BitShares UI wiki
 - [How do I get the network_add_nodes command to work? Why is it so complicated?](../tutorials/FAQ.md#q-how-do-i-get-the-network_add_nodes-command-to-work-why-is-it-so-complicated)
 - [Is there a way to access methods which require login over HTTP?](../tutorials/FAQ.md#q-is-there-a-way-to-access-methods-which-require-login-over-http)
 - [Is there a way to allow external program to drive cli_wallet via websocket, JSONRPC, or HTTP?](../tutorials/FAQ.md#q-is-there-a-way-to-allow-external-program-to-drive-cli_wallet-via-websocket-jsonrpc-or-http)
 - [Is there a way to generate help with parameter names and method descriptions?](../tutorials/FAQ.md#q-is-there-a-way-to-generate-help-with-parameter-names-and-method-descriptions)
 - [What is the meaning of a.b.c numbers?](../tutorials/FAQ.md#q-what-is-the-meaning-of-abc-numbers)
 - [The answer to the previous question was really confusing. Can you make it clearer?](../tutorials/FAQ.md#q-the-answer-to-the-previous-question-was-really-confusing-can-you-make-it-clearer)
 - [How to process Account Registration](../accounts/account_registration.md#account-registration)



***

