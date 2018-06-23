After the Installation. 
successfully, `make`

You installed BitShares Core successfully! 

## Witness Node

You have now the witness_node executable and the cli_wallet executable. Both take various parameters. We are going to talk about each operation to see the BitSharea Core Overview. 

> **Note:** A witness node is identical to a full node if no authorized block-signing private key is provided

After the installation, you might want to see and "interact" with the decentralized network (blockchain). 

In your terminal, you can execute the witness_node (see the below command), the comment will launch the witness node and automatically create a data directory including a config.file and start downloading the data to fully synchronize the blockchain. Without any parameter, you would get a history of the blockchain data. 

You might see the downloading data on your screen. It will take several hours to sync and you might want to prepare a better hardware before you run this process.

      ./programs/witness_node/witness_node

#### API node, config.ini, and plugins
      
The witness_node takes parameters (see a [config.file](/core/nodes_full_witness/full_nodes.md#configuration) file) and [`plugins`](/forge/plugins/nodes_memory_reduction.md#memory-reduction-for-nodes) to reduce the download data size.

For example, you can add `--rpc-endpoint` parameter to run basic API Node. The below command starts a node to listen for api calls at port 8090 (localhost). The `--rpc-endpoint` value can be changed to another IP address or local network.

	./programs/witness_node/witness_node --rpc-endpoint 127.0.0.1:8090


Next example shows how to set [`plugins`](/forge/plugins/nodes_memory_reduction.md#memory-reduction-for-nodes) to reduce the download data size and how to specify a data directory (e.g., data/my-blockprod) by a parameter `--data-dir`. 

		
	./programs/witness_node/witness_node --data-dir data/my-blockprod 
	                                     --rpc-endpoint "127.0.0.1:8 
					     --max-ops-per-account 1000         
					     --partial-operations true

> **Note:** # `--max-ops-per-account 1000` means --> 80 Gigs of RAM to run 


#### network configuration

The BitShares client uses a peer-to-peer(p2p) network to connect and broadcast transactions. We saw previously the witness_node (full_node) accept parameter(`--rpc-endpoint`) and connect to the network directly. We call it **trusted** ([Trusted Full Node](/core/wallet/wallet_network.md#trusted-full-node)) since it is supposed to be under our control.

In the BitShares, there are two types of network configuration setups. For general purpose setups, we recommend a reduced complexity setup (i.e., [General Setup](/core/wallet/wallet_network.md#general-network-and-wallet-configuration)). For high security, we provide a so called [**delayed** full node](/core/wallet/wallet_network.md#delayed-full-node) which accepts the parameter trusted-node for an RPC endpoint of a trusted validating node (i.e., [Secure Network Setup](/core/wallet/wallet_network.md#secure-network-and-wallet-configuration)). 

Differences between the trusted full node and the delayed full node would be the trusted full node is exposed to the internet and the delayed full node is not exposed to the internet. 


#### public full node
Any BitShares application (gateway, explorer, wallet, trading program, etc) interacts with the decentralized network (blockchain) are connecting to one or many API Nodes. If you plan to run a business on top of BitShares, you will probably want one or several API nodes of your property. Or, you can use a trusted **public API server** to interact the blockchain. Here is a latest list of [**Public Full Node**](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information.

#### genesis.json
										 
If you are interested to use BitShares Testnets later, you will learn about a "genesis.json" file which you can set up the initial state of the network. You will create "my-genesis.json" file to customize to match your needs. (i.e.,[Genesis Block](https://github.com/bitshares/bitshares-core/blob/testnet/genesis.json) - for Testnet).

Here is more information about How to setup BitShares [Private Testnet](/core/testnets/private_testnet.md#3-creating-a-genesis-file-for-a-private-testnet) and BitShares [Public Testnet](/core/testnets/public_testnet.md#2-genesis-configuration). 


So far, we are learning about the **node** which connect to the network directory and take the parameters. BitShare has two levels of network setups (trusted full node and delayed full node) for businesses. If you do not have own node to run, you can use the public api server (public full node). 

***

Next, we will talk about wallet (cli-wallet).

The [wallet](/core.dev/blob/master/core/wallet/wallet_network.md#network-and-wallet-configuration) is used to initiate transfer and connects to the trusted full node. 


## Wallet

Once we know which witness_node (full_node) or the public API node to use. We can use the full node to open up a cli_wallet. 

The cli_wallet creates a local `wallet.json` file that contains the encrypted private keys required to access the funds in your account. The wallet (cli_wallet) requires a running full node to connect to because it does not offer P2P or blockchain capabilities directly.

**Example - [General Network Setup](https://github.com/cedar-book/core.dev/blob/master/core/wallet/wallet_network.md#general-network-and-wallet-configuration) <Trusted Full Node>**

Let's look at some examples, first we start the witness_node with `--rpc-endpoint`. Next we use the `--rpc-endpoint` as a `--server-rpc-endpoint` and open up a RPC-JSON-HTTP port to be able to interface with API request. 

	./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"

	./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                     --rpc-http-endpoint="192.168.0.102:8092"


If you open the cli-wallet successfully, you will receive `new >>>` prompt to set a password.
Before continuing, let's set a password. This password is used to encrypt the private keys in the wallet.
After unlock the cli-wallet, you can issue any command available to the cli-wallet ([Wallet APIs](https://bitshares.org/doxygen/classgraphene_1_1wallet_1_1wallet__api.html)) or [construct your own transaction manually](/core/tutorials/trn_construct_transaction.md#how-to-construct-any-transaction---manually).

	new >>> set_password supersecret123
	set_password supersecret123
	null
	locked >>> 
										 
	locked >>> unlock "supersecret123"
	unlock "supersecret123"
	null
	unlocked >>>


**Example - [Secure Network Setup](https://github.com/cedar-book/core.dev/blob/master/core/wallet/wallet_network.md#secure-network-and-wallet-configuration)  <Trusted Full Node + Delayed Full Node>**

Let's close look at some examples, first we start the witness_node with `--rpc-endpoint`. Next we use the `--rpc-endpoint` (in witness_node) as a `--trusted-node` and set the Delayed Full node as the `--rpc-endpoint` value. 


	./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"

	./programs/witness_node/witness_node --trusted-node="192.168.0.100:8090" \
                                         --rpc-endpoint="192.168.0.101:8090"
                                         -s "0.0.0.0:0" \
                                         --p2p-endpoint="0.0.0.0:0" \
                                         --seed-nodes "[]"

	./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                         --rpc-http-endpoint="192.168.0.102:8092"


####  Websocket RPC / HTTP RPC 

In order to allow RPC calls for wallet operations (spend, buy, sell, …) you can choose between pure RPC or RPC-HTTP requests

The cli-wallet can open a RPC port so that you can interface your application with it. You have the choices of


You have the choices of websocket RPC via the `-r` parameter, and HTTP RPC via the `-H` parameter. 

You can choose between Websocket RPC or RPC-HTTP requests. You can also set both ports together (below example) and interface your application with it.

	./programs/cli_wallet/cli_wallet --wallet-file my-wallet.json 
                               -s ws://127.0.0.1:11011 
                               -H 127.0.0.1:8090 
                               -r 127.0.0.1:8099	
	
> Note: The parameter -H is required so that we can interface with the cli-wallet via RPC-HTTP-JSON, later while -r will open a port for the websocket RPC.


	
***

### API





Wallet specific commands, such as transfer and market orders, are only available if connecting to cli_wallet because only the wallet has the private keys and signing capabilities and some calls will only execute if the wallet is unlocked.

The full node offers a set of API(s), of which only the database calls are available via RPC. Calls that are restricted by default (i.e. network_node_api) or have been restricted by configuration are not accessible via RPC because a stateful protocol (websocket) is required for login.



If you also want to run a wallet, please pick reasonable different ports and make sure you do not try to call methods at the wallet that are only available to the blockchain API.

RPC calls are state-less and accessible via regular JSON formatted RPC-HTTP-calls

Websocket calls are stateful and accessible via regular JSON formatted websocket connection.

### API Access restriction


objects and IDs


***


### Account



****
			


***
***
	



*****
