## Install a Development Environment

BitShares offers you to install BitShares-Core into different platforms; **Ubuntu (x64), OSx, and Windows**.  There are  dependencies to check when you download OpenSSL and  Boost. Please make sure which versions you downloaded. 

Also, if you are a Windows user, you have other two choices to install BitShares Core to your Windows (x64) Operation System.  One is **CLI-Wallet tools for Windows (x64)**, another is **Windows SubSystem for Linux (WSL)**.  

The CLI-wallet tools for Windows (x64) allows you to try several methods to get the data from BitShares blockchain without the BitShares Core installation. After you download the cli-wallet tools, you will find a cli_wallet executable to try Wallet API. 

Another option, Windows SubSystem for Linux (WSL).  This is for a developer who uses a Windows 10 (x64) Operation System and wants to build BitShares Core on Ubuntu.  

## Installation 

- [Ubuntu Linux](/core/installation/build_ubuntu.md#building-on-ubuntu)
- [OS X](/core/installation/build_osx.md#building-on-os-x)
- [Windows](/core/installation/build_windows.md#building-on-windows)
- [Windows - CLI Tools](/core/installation/windows_cli_tool.md#cli-wallet-on-windows-x64)

### Build and Run BitShares-Core in WSL (Installation Option)
- Use **Windows Subsystem for Linux**
  - [Instruction how to prepare Ubuntu to Windows 10 OS](/core/installation/wsl.md#windows-subsystem-for-linux-wsl) 

***

### Known issues

- [Boost versions](/core/installation/boost_versions.md#boost-version)


***
***

# After the Installation
#### Table of contents:
- [Nodes](#nodes)
- [Witness/Full Nodes](#witness-node)
- [Network Configurations](#network-configuration)
- [Wallet](#wallet)
- [API](#api)
- [Objects and IDs](#objects-and-ids)
- [Accounts](#accounts)
-

***

You installed BitShares Core successfully!

After you installed BitShares Core, you have `witness_node.exe` and `cli_wallet.exe` to run.  Both executables pass various parameters and set a configuration file (i.e., `config,ini` , `wallet.json`). 

We will talk about each operation to understand the BitShares Core Overview operations.

***

## Nodes
First, we need to connect to the network to interact to the BitShares Blockchain. The Nodes have the role. The nodes in the network both verify all transactions and blocks against the current state of the overall network (i.e., broadcast messages across a network). 

#### [Types of nodes](/core/nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)

First, you might've noticed, we use `witness node` and 'full node' terms almost exchangeable.  But there are some differences.  We distinguish the Nodes between full nodes (a.k.a. **non-block producing** witness nodes) and **block producing** witness nodes. 

Both are implemented by the same executable, but the additional parameters can define each node operation difference.  For example, a (block producing) witness node has been provided an authorized block-signing private key, on the other hand, a (non-block producing) full node is not.  But both "interact" with the decentralized network (blockchain). They should maintain their own full nodes secure and reliable.


## Witness Node

#### [Running a BitShares API node](/core/nodes_full_witness/running-api-node.md#running-a-bitshares-api-node)

In your terminal, you can execute the witness_node (see the below command), the command will launch the witness node and automatically create a data directory including a [config.file](/core/nodes_full_witness/full_nodes.md#configuration). And the operation starts downloading the data to fully synchronize the blockchain if you start without any parameters. 

      ./programs/witness_node/witness_node

You might see downloading the data (i.e., a history of the blockchain data ) on your screen. It will take several hours to sync.  You might want to prepare a better  hardware before you run this process if you would like to receive the data. 

If you want to avoid downloading the history of the BitShares blockchain data, you can set some plugins - [Memory Reduction for Nodes](/forge/plugins/nodes_memory_reduction.md#memory-reduction-for-nodes). 

#### API node, config.ini, and plugins

The witness_node takes parameters (see a [config.file](/core/nodes_full_witness/full_nodes.md#configuration) items list) and plugins in the command line. You can add parameter values into the config.ini also. Examine the default config.ini to know how you can customize it. 

For example, you can add `--rpc-endpoint` parameter and  a value to run basic API Node. The below command starts a node to listen for api calls at port 8090 (localhost). The `--rpc-endpoint` value can be changed to another IP address or local network.

	./programs/witness_node/witness_node --rpc-endpoint 127.0.0.1:8090


Next example shows how to set plugins to reduce the synchronizing data size and how to specify a data directory (e.g., data/my-blockprod) by using a parameter `--data-dir`. 
		
	./programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                     --rpc-endpoint "127.0.0.1:8 
					     --max-ops-per-account 1000         
					     --partial-operations true
					     
					    
Above plugins limit the operations per account 1000 to save RAM. For this operation, a full node needs  more than 80 gigs of RAM to run.

#### public full node

So far, we talked about the Nodes which connect to the network directory and checked the parameters in  the config.ini to customize your connection. This pattern requires IP address to set the endpoint. 

If you do not have own node to run, you can use the public API server (public full node) to connect the BitShares blockchain. Here is a latest list of [Public Full Node](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information.


## Network Configuration

The BitShares client uses a peer-to-peer(p2p) network to connect and broadcast transactions
For businesses, BitShares offers two patterns of network nodes ([trusted full node](/core/wallet/wallet_network.md#general-network-and-wallet-configuration) and [delayed full node](/core/wallet/wallet_network.md#delayed-full-node) ).

In this section, we will explain about the Network Configurations of BitShares.

#### [Trusted full node](/core/wallet/wallet_network.md#general-network-and-wallet-configuration)

We saw, in the previous section, the witness_nodes (full_nodes) accept parameters (e.g., `--rpc-endpoint`) and connect to the network directly. We call it trusted (Trusted Full Node) since it is supposed to be under our control.

In the BitShares, there are two types of network configuration setups. For general purpose setups, we recommend a reduced complexity setup (i.e., General Setup). For high security, we provide a so called delayed full node which accepts the parameter trusted-node for an RPC endpoint of a trusted validating node (i.e., Secure Network Setup).

In the both settings, the trusted full node is your entry point to the BitShares P2P network.  It will hold the blockchain, connect to other peers, and will receive new blocks in real-time.

#### [Delayed full node](/core/wallet/wallet_network.md#delayed-full-node)

The delayed full node is used for the Secure Network setup.  The delayed full node will provide us with a delayed and several times confirmed and verified blockchain.  How long is that's depending on the block interval and the number of witnesses, this may lead to a few minutes of delay. The delayed node will delay blocks until they are permanent.  Therefore,  all transactions that are confirmed by the delayed node are irreversible


Any BitShares application (gateway, explorer, wallet, trading program, etc) interacts with the decentralized network (blockchain) are connecting to one or many API Nodes. If you plan to run a business on top of BitShares, you will probably want one or several API nodes of your property.  Also, if you are planning to create an exchange, that should only interface with the delayed full node for the security reasons.
 
If you plan to run a business on top of BitShares, you will probably want one or several API nodes of your property.  Also, if you are planning to create an exchange, that should only interface with the delayed full node for the security reasons.


 
****


****



Next, we will talk about wallet (cli-wallet). The Nodes are connected to the network and handle the transactions and block produces. The [wallet](/core/wallet/wallet_network.md#components) is used to initiate transfer and connects to the trusted full node. 


## Wallet

The Nodes are connected to the network and verify all transactions and block produces. The cli_wallet is used to initiate transfer and connects to the trusted full node..

The cli_wallet creates a local [`wallet.json`](/core/wallet/cli_wallet.md#overview) file that contains the encrypted private keys.  The key is required to access the funds and add new data to the blockchain with a signature from a private key. 

Connecting the cli_wallet requires a running full node (not necessarily locally) and connect to it. You might have own node to use. If you do not have it, you can select one of [BitShares Public Full Nodes](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67), or nodes that run by _businesses_ or _individuals_.



**Example: wallet.json**

      {
        "chain_id": "4018d7844c78f6a6c41c6a552b898022310fc5dec06da4222222",
        "my_accounts": [],
        "cipher_keys": "4144454a976266ed15f736df4f5645e60bace86eb87cb5b59b0c8f48b75c6131167807c403a56060528b7dae993de667736d5ab9ef1f60fb340c4aa70437ec7a2534bbdab051b9d2d1871111111",
        "extra_keys": [],
        "pending_account_registrations": [],
        "pending_witness_registrations": [],
        "labeled_keys": [],
        "blind_receipts": [],
        "ws_server": "ws://localhost:8090",
        "ws_user": "",
        "ws_password": ""
      }


### Connecting a Wallet 

In the previous section, we talked about General and High Security Network configurations about the Nodes.  Our next examples show how we can connect and open a cli_wallet by using the Node after the wetness_node connected. 

**Example 1 - [General Network Setup](https://github.com/cedar-book/core.dev/blob/master/core/wallet/wallet_network.md#general-network-and-wallet-configuration) <Trusted Full Node>**

Let's look at this examples, first we start the witness_node with `--rpc-endpoint`. Next we use the `--rpc-endpoint` as a `--server-rpc-endpoint` in the cli_wallet command line and open up a RPC-JSON-HTTP port to be able to interface with API request. 

**witness_node**

	./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"

**cli_wallet**

	./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                     --rpc-http-endpoint="192.168.0.102:8092"


If you open the cli-wallet successfully, you will receive `new >>>` prompt to set a password. The below are example output. Use a `set_password` to set a password. And after set the password, use a `unlock` command to unlock the wallet. 

After unlock the cli-wallet, you can issue any command available to the cli-wallet ([Wallet APIs](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html)) or [construct your own transaction manually](/core/tutorials/trn_construct_transaction.md#how-to-construct-any-transaction---manually).

> The password is used to encrypt the private keys in the wallet.

	new >>> set_password supersecret123
	set_password supersecret123
	null
	locked >>> 
										 
	locked >>> unlock "supersecret123"
	unlock "supersecret123"
	null
	unlocked >>>


**Example 2- [Secure Network Setup](https://github.com/cedar-book/core.dev/blob/master/core/wallet/wallet_network.md#secure-network-and-wallet-configuration)  <Trusted Full Node + Delayed Full Node>**

Let's close look at the next examples, first we start the witness_node with `--rpc-endpoint`. Next we use the `--rpc-endpoint` (in witness_node) as a `--trusted-node` and set the Delayed Full node as the `--rpc-endpoint` value in the cli_wallet command line.

**witness_node**

	./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"

	./programs/witness_node/witness_node --trusted-node="192.168.0.100:8090" \
                                         --rpc-endpoint="192.168.0.101:8090"
                                         -s "0.0.0.0:0" \
                                         --p2p-endpoint="0.0.0.0:0" \
                                         --seed-nodes "[]"
**cli_wallet**

	./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                         --rpc-http-endpoint="192.168.0.102:8092"


###  [Websocket RPC / HTTP RPC](/core/wallet/cli_wallet.md#case-1-connecting-a-cli-wallet---the-public-api-server-node)
The cli-wallet can open a RPC port so that you can interface your application with it. You have the choices of websocket RPC via the `-r` parameter, and HTTP RPC via the `-H` parameter. 

You can choose between [Websocket RPC](/core/api/websocket_calls_notifications.md#websocket-calls-notifications) or [RPC-HTTP](/core/api/rpc.md#remote-procedure-calls) requests, and also can set both ports together (below example).

	./programs/cli_wallet/cli_wallet --wallet-file my-wallet.json 
                               -s ws://127.0.0.1:11011 
                               -H 127.0.0.1:8090 
                               -r 127.0.0.1:8099	

> **Note:** For security reasons, the wallet should only listen to localhost or the local network and should NEVER be exposed to the internet.	

### [Use a Public API Node](/core/wallet/cli_wallet.md#case-2-connecting-a-cli-wallet---a-node-ip-address)

We show you how you can use the Public API Node in your `cli_wallet` command line. 

- You can find a latest list of [Public Full Node](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information. 

**Example:**  We use the public API node of OpenLedger `wss://bitshares.openledger.info/ws` and connect via secured websocket connection:

	./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws


This will open the cli-wallet and unless you already have a local wallet, it will ask you to provide a password for your local wallet. Once a wallet has been created (default wallet file is a wallet.json), you will receive,

	>>> new

We mentioned before, you can set a password and unlock the wallet. After unlocked the wallet, you can issue [`Wallet API calls`](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html).

### About Wallet API Calls

The Wallet APIs include Wallet Calls, Account Calls, Transaction Calls, Asset Calls, Governance, Privacy Mode, Blockchain Inspection, and Transaction Builder. After unlocked the wallet, you can gain access to the wallet by importing keys, registering an account, and transferring  funds. [Here](/core/wallet/cli_wallet.md#gaining-access-to-blockchain) is some example  steps to gain the access and about an [Account Registration](/core/accounts/account_registration.md#account-registration).

**Note:** To register an account, the registrar needs to be a **lifetime member (LTM)**. You can upgrade the account to Lifetime member (LTM) status. 


***
***

## API

Now, let's look into [BitShares APIs](/core/api/apis-about.md#apis-categories). APIs are separated into two categories; Blockchain API and Wallet API. 

- **[Blockchain API](https://bitshares.org/doxygen/namespacegraphene_1_1app.html):** It's used to query blockchain data (account, assets, trading history, etc).
- **[Wallet API](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html):** It has your private keys loaded and is required when interacting with the blockchain with new transactions.

The set of available calls depend on whether you connect to the full node (witness_node) or the wallet (cli_wallet). Both support [RPC-JSON](/core/api/rpc.md#remote-procedure-calls). The full node also supports the [websocket protocol](/core/api/websocket_calls_notifications.md#websocket-calls-notifications) with [notifications](/core/api/websocket_calls_notifications.md#database-notifications).  


There are wallet specific commands, such as transfer and market orders. They are only available after connecting to the cli_wallet because only the wallet has the private keys and signing capabilities. And some of calls will execute if the wallet has been unlocked.


### [API Access Restriction](/core/api/api_restrictions.md#api-access-and-restrictions)

Access to some API calls are restricted by default (i.e. network_node_api) or have been restricted by configuration([config.ini](/core/nodes_full_witness/full_nodes.md#configuration)). The restricted API calls are not accessible via RPC because requires login, thus it is only accessible over the websocket RPC (i.e., [API Access Procedure](/core/api/websocket_calls_notifications.md#api-access-procedure) ).

We provide several different API's. Each API has its own ID. When running witness_node, initially two API's are available.

- **API 0 provides read-only access to the database. (e.g. state-less RPC-calls querying)**
- **API 1 is used to login and gain access to additional, restricted API's. (e.g. authenticated interaction)**

>Note: To access the restricted API-1; we are required to use the websocket connection with callbacks to access API-1

For sensitive businesses that want to ensure that deposits are irreversible, we recommend the use of the High Security Setup. That contains a delayed node to pass only irreversible transactions to the API.


### [Objects and IDs](/core/api/object_ids.md#objects-and-ids)

We will use the Objects and IDs when we search the BitShares Blockchain. 

BitShares 2.0 has a different model to represent the blockchain(space), its transactions(type) and accounts(unique ids). On the BitShares blockchains there are **no addresses**, but objects identified by a **unique id, an type and a space**.

**BitShares Objects Format**

		space.type.id

- [List of Commonly used Objects](/core/api/object_ids.md#list-of-commonly-used-objects)

In the protocol space, there are raw objects such as, accounts, assets, committee members as well as orders, proposals and balances. The implementation space is used to gain access to higher abstraction layers for instance content of the current database state (these include, current global blockchain properties, dynamic asset data, transaction histories as well as account statistics and budget records).

**Examples1: account object (1.2.x)**

		unlocked >>> get_account_id "user123"
		get_account_id "user123"
		"1.2.539269"
		unlocked >>>


**Examples2: asset object (1.3.x)**

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

		
****		
****

(work in progress...)

****


## Accounts



****
