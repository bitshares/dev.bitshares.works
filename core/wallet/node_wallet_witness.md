## A Collection of command lines - Node, Wallet, and Witness

This page is a collection of command lines to connect Node, Wallet, and Witness. You can compare their differences to understand the patterns and how they are used.

#### Table of Contents:
- [Launch the full node](/core/wallet/node_wallet_witness.md#launch-the-full-node)
- [Run the witness as a node in the network (localhost):](/core/wallet/node_wallet_witness.md#run-the-witness-as-a-node-in-the-network-localhost)
- [Run the witness as a node in the network](/core/wallet/node_wallet_witness.md#run-the-witness-as-a-node-in-the-network)
- [Use the Public API node](/core/wallet/node_wallet_witness.md#use-the-public-api-node)
- [Public Testnet - how to create/connect a cli_wallet](/core/wallet/node_wallet_witness.md#public-testnet---how-to-createconnect-a-cli_wallet)
- [Private Testnet](/core/wallet/node_wallet_witness.md#private-testnet)
- [General Network and Wallet Configuration](/core/wallet/node_wallet_witness.md#general-network-and-wallet-configuration)
- [Secure Network and Wallet ](/core/wallet/node_wallet_witness.md#secure-network-and-wallet)


*** 

**Note:** The *CLI client* is unable to run on its own (i.e. without being connected to the witness node). So successfully to run the CLI client you need to do...

- make sure you have uncommented this entry `rpc-endpoint = 127.0.0.1:8090` in the `witness_node_data_dir/config.ini` file.
- before you start the CLI client, you need to start the witness node (and wait a while till it’s up and running).

**Note:** After you start running the CLI client, you can issue any command avaiable to the cli-wallet (Wallet API) or construct your own transaction manually.

***
<E.g. 1: [(source)](/core/nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)>

### Launch the full node:  


    ./programs/witness_node/witness_node

- Read about [witness_node: **config.ini** settings](/core/nodes_full_witness/full_nodes.md#how-to-launch-the-full-node).  

At this point, you can try queries to the blockchain. (i.e., Database APIs. *exclude wallet APIs*)

**Note:** A witness node is identical to a full node if no authorized block-signing private key is provided.

***
<E.g. 2 [(source)](/core/nodes_full_witness/active_witness.md#2-create-a-cli-wallet)>
### Run the witness as a node in the network (localhost):

    ./programs/witness_node/witness_node --rpc-endpoint 127.0.0.1:8090
    
**Create a Wallet (2a)**:

This will open the port 8090 for local queries only. 

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090


**Create a Wallet (2b)**: 

This will open the port 8091 for HTTP-RPC requests and has the capabilities to handle accounts while the witness_node can only answer queries to the blockchain.

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090
                                    -H 127.0.0.1:8091

*It is not recommended to publicly expose your wallet.*

***
<E.g. 3 [(source)](/core/nodes_full_witness/full_nodes.md#enabling-remote-procedure-calls-rpc)>
### Run the witness as a node in the network:

    ./programs/witness_node/witness_node --rpc-endpoint="0.0.0.0:8090"

This will open port 8090 and make it available over the internet (unless you run behind a router/firewall). 
If you want it to be open for your machine only, replace 0.0.0.0 by localhost.

***
<E.g. 4 [(source)](/core/wallet/cli_wallet.md#case-1-connecting-a-cli-wallet)>
### Use the Public API node 

- Here is the latest list of [Public Full Node](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information

**Create a Wallet (4a)**: 

This pattern uses the Public API node of *OpenLedger* and connect via secured websocket(wss) connection. Open the cli-wallet,

    ./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws

**Create a Wallet (4b)**:

Cli-wallet Open a Remote Procedure Call (RPC) Port for Wallet Operation (spend, buy, sell...).

You can choose between Websocket RPC or RPC-HTTP requests. You can also set both ports together (*below example*) and interface your application with it. 

    ./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws 
                                     -H 127.0.0.1:8092 
                                     -r 127.0.0.1:8093                                                                        

- Websocket RPC via the `-r` parameter, and
- HTTP RPC via the `-H` parameter

***
<E.g. 5 [(source)](/core/testnets/public_testnet.md#4-connecting-a-cli-wallet)>
### Public Testnet - how to create/connect a cli_wallet

In order to create a wallet (on Public Testnet), you must specify the previously setup server (e.g., `-s ws://127.0.0.1:11011`) with the witness node’s default access control settings.

    ./programs/cli_wallet/cli_wallet --wallet-file my-wallet.json 
                                    -s ws://127.0.0.1:11011 
                                    -H 127.0.0.1:8090 
                                    -r 127.0.0.1:8099
                                   
**Note:** The parameter `-H` is required so that we can interface with the cli-wallet via `RPC-HTTP-JSON`, later while `-r` will open a port for the websocket RPC.

***
<E.g. 6 [(source)](/core/testnets/private_testnet.md#10-creating-a-new-wallet)>
### Private Testnet 

This connects the cli client to your testnet witness node. Keep your witness node running and in another Command Prompt window run this command:

    ./programs/cli_wallet/cli_wallet --wallet-file=my-wallet.json 
                                    --chain-id=8b7bd36a146a03d0e5d0a971e286098f41230b209d96f92465cd62bd64294824 
                                    --server-rpc-endpoint=ws://127.0.0.1:8090

Depending on the actual chain that you want to connect to your may need to specifiy –chain-id.

(ref) [Creating a New Wallet](/core/testnets/private_testnet.md#10-creating-a-new-wallet) on Private Testnet

***
***
<E.g. 7 [(source)](/core/wallet/wallet_network.md#general-network-and-wallet-configuration)>
### General Network and Wallet Configuration 

**settings** 

- Trusted Full Node:
  - extern: internet access **required**
  - intern: 192.168.0.100
  - port: 8090

- Wallet:
  - extern: **no** internet access required
  - intern: 192.168.0.102
  - port: 8092

**Trusted Full Node**

For the trusted full node, the default settings can be used. For later, we will need to open the RPC port and listen to an IP address to connect the wallet to.

    ./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"

**Note:** A witness node is identical to a full node if no authorized block-signing private key is provided.

**Wallet**

We open up a RPC-JSON-HTTP port to be able to interface with API requests. The wallet can be connected to the trusted node and listens for RPC requests on port `8092` with:

    ./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                    --rpc-http-endpoint="192.168.0.102:8092"

**Note:** For customer withdrawals, we will interface to the wallet’s API using 192.168.0.102:8092.


***
<E.g. 8 [(source)](/core/wallet/wallet_network.md#secure-network-and-wallet-configuration)>
### Secure Network and Wallet 

**settings** 

- Trusted Full Node:
  - extern: internet access **required**
  - intern: 192.168.0.100
  - port: 8090 
  
- Delayed Full Node:
  - extern: **no** internet access required
  - intern: 192.168.0.101
  - port: 8090

- Wallet:
  - extern: **no** internet access required
  - intern: 192.168.0.102
  - port: 8092 
 
 **Trusted Full Node**
 
For the trusted full node, the default settings can be used. For later, we will need to open the RPC port and listen to an IP address to connect the delayed full node to.

    ./programs/witness_node/witness_node --rpc-endpoint="192.168.0.100:8090"

 **Delayed Full Node**
 
The delayed full node will need the IP address and port of the p2p-endpoint from the trusted full node and the number of blocks that should be delayed. We also need to open the RPC/Websocket port (to the local network!) so that we can interface using RPC-JSON calls.

    ./programs/witness_node/witness_node --trusted-node="192.168.0.100:8090" \
                                        --rpc-endpoint="192.168.0.101:8090"
                                        -s "0.0.0.0:0" \
                                        --p2p-endpoint="0.0.0.0:0" \
                                        --seed-nodes "[]"
                                     
We could now connect via RPC:

- `192.168.0.100:8090` : The trusted full node exposed to the internet
- `192.168.0.101:8090` : The delayed full node **not** exposed to the internet

**Note:** For security reasons, an exchange should only interface with the delayed full node.

For obvious reasons, the trusted full node should be running before attempting to start the delayed full node.
For customer deposits, we will interface to the delayed node’s API using 192.168.0.101:8090.

**Wallet**

We open up a RPC-JSON-HTTP port to be able to interface with API requests. The wallet can be connected to the trusted node and listens for RPC requests on port `8092` with:

    ./programs/cli_wallet/cli_wallet --server-rpc-endpoint="ws://192.168.0.100:8090" \
                                    --rpc-http-endpoint="192.168.0.102:8092"

**Note:** For security reasons, the wallet should only listen to localhost or the local network and should NEVER be exposed to the internet.

For customer withdrawals, we will interface to the wallet’s API using 192.168.0.102:8092

***

