## BitShares API

#### Table of Contents: 
- [BitShares-Core API References](/core/api/API_references.md#bitshares-core-api-references---doxygen-documentation-quick-links)
- [API Access & Restrictions](/core/api/api_restrictions.md#api-access-and-restrictions)
- [Objects and IDs](/core/api/README.md#objects-and-ids)
- [Available Calls](/core/api/README.md#available-calls) 
- [FAQ - API](/core/api/README.md#faq---api)

***

BitShares APIs are available by using [Remote Procedure Calls (RPC)](/core/api/rpc.md#remote-procedure-calls) and [WebSocket Calls & Notifications](/core/api/websocket_calls_notifications.md#websocket-calls-notifications).  All API calls are formatted in JSON and return JSON only.

There are [API Access Restrictions](/core/api/api_restrictions.md#api-access-and-restrictions). ( API 0 : state-less RPC-calls querying.  API 1: authenticated interaction by WebSocket.)  And you can restrict API's to particular users by setting the information into an [`api-access.json` ](/core/api/api_restrictions.md#accessing-restricted-apis).  You create the api-access.json file and specify a path to the api-access.json file in [`config.ini`](/core/nodes_full_witness/full_nodes.md#configuration) in the data directory. 

BitShares APIs can categorize two; [Blockchain API](https://bitshares.org/doxygen/namespacegraphene_1_1app.html) and [Wallet API](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html). Blockchain API is used to query blockchain data  (account, assets, trading history, etc).  And Wallet API is used to interact with the blockchain operations (create an account, sign, transfer, market orders, etc). The wallet has the private keys and signing capabilities, so, Wallet specific commands are only available if the wallet is connecting to cli_wallet and is unlocked. 

BitShares uses [Objects and IDs model](/core/api/object_ids.md#objects-and-ids) to represent the blockchain. The BitShares blockchains do not have the addresses that most cryptocurrency wallets have, instead each object is identified by a unique id, a type, and space. (i.e., BitShares Objects Format: space.type.id.  -- 1.1.x: base object, 1.2.x:account object, 1.3.x:asset object, etc)  

***

### [BitShares-Core API References](/core/api/API_references.md#bitshares-core-api-references---doxygen-documentation) - Doxygen 
Blockchain APIs, Wallet API Calls, Objecs IDs, and objects information
***

## API Access & Restrictions
   - [Using the API](/core/api/api_restrictions.md#using-the-api)
   - [Accessing Restricted API's ](/core/api/api_restrictions.md#accessing-restricted-apis)
   - [Accessing the Named API via HTTP](/core/api/api_restrictions.md#accessing-the-named-api-via-http)
   - [Login In](/core/api/api_restrictions.md#login-in)

## Objects and IDs
BitShares-Core uses a different model to represent the blockchain(space), its transactions(type) and accounts(unique ids). On the BitShares blockchains there are no addresses, but objects identified by a unique id, a type, and space.

**BitShares Objects Format**

      space.type.id

- [List of Commonly used Objects](/core/api/object_ids.md#objects-and-ids)   
BitShares objects were identified by a unique id, an type and a space. You should know some of them.
   
   
**Example Call and a result: account object (1.2.x)**

		unlocked >>> get_account_id "user123"
		get_account_id "user123"
		"1.2.539269"
		unlocked >>>


**Example Call and a result: asset object (1.3.x)**

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
- [Remote Procedure Calls (RPC)](/core/api/rpc.md#remote-procedure-calls)
- [Websocket Calls & Notifications](/core/api/websocket_calls_notifications.md#websocket-calls-notifications)



## FAQ - [API](/core/tutorials/FAQ.md#apis)
 - [How an Exchange can integrate their services to the BitShares UI Wallet - Gateway Integration Requirements](https://github.com/bitshares/bitshares-ui/wiki/Gateway-Integration-Requirements) : from BitShares UI wiki
 - [How do I get the network_add_nodes command to work? Why is it so complicated?](/core/tutorials/FAQ.md#q-how-do-i-get-the-network_add_nodes-command-to-work-why-is-it-so-complicated)
 - [Is there a way to access methods which require login over HTTP?](/core/tutorials/FAQ.md#q-is-there-a-way-to-access-methods-which-require-login-over-http)
 - [Is there a way to allow external program to drive cli_wallet via websocket, JSONRPC, or HTTP?](/core/tutorials/FAQ.md#q-is-there-a-way-to-allow-external-program-to-drive-cli_wallet-via-websocket-jsonrpc-or-http)
 - [Is there a way to generate help with parameter names and method descriptions?](/core/tutorials/FAQ.md#q-is-there-a-way-to-generate-help-with-parameter-names-and-method-descriptions)
 - [What is the meaning of a.b.c numbers?](/core/tutorials/FAQ.md#q-what-is-the-meaning-of-abc-numbers)
 - [The answer to the previous question was really confusing. Can you make it clearer?](/core/tutorials/FAQ.md#q-the-answer-to-the-previous-question-was-really-confusing-can-you-make-it-clearer)
 - [How to process Account Registration](/core/accounts/account_registration.md#account-registration)



***

