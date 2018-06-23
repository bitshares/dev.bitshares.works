## Wallet Full Nodes & Witness Nodes

*This page shows example command lines a client to connect to the network and open up a node.*


***

## Introduction to Wallet/Node 
First, we need a client to connect to a witness (we run in monitor mode) or a public trusted witness. For exchanges, it is recommended to run a full node, though. In this example, we connect to the network via a seed node:

      programs/witness_node/witness_node -s 104.200.28.117:61705 --rpc-endpoint 127.0.0.1:8090  # FIXME?

This opens up a node that we can connect to via the included wallet

      programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090 -H 127.0.0.1:8091

which will 
- open port `8091` for HTTP-RPC requests and 
- has the capabilities to handle accounts while the `witness_node` can only answer queries to the blockchain.

***

## Example

### Output - Core Dev

            root@NC-PH-1346-07:~/bitshares/issue25/bitshares-core# programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090
            Logging RPC to file: logs/rpc/rpc.log
            2409841ms th_a       main.cpp:120                  main                 ] key_to_wif( committee_private_key ): 5KCBDTcyDqzsqehcb52tW5nU6pXife6V2rX9Yf7c3saYSzbDZ5
            W 
            2409841ms th_a       main.cpp:124                  main                 ] nathan_pub_key: BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV 
            2409841ms th_a       main.cpp:125                  main                 ] key_to_wif( nathan_private_key ): 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3 
            Starting a new wallet with chain ID 4018d7844c78f6a6c41c6a552b898022310fc5dec06da467ee7905a8dad512c8 (from egenesis)
            2409843ms th_a       main.cpp:172                  main                 ] wdata.ws_server: ws://127.0.0.1:8090 
            0 exception: unspecified
            Underlying Transport Error
                {"message":"Underlying Transport Error"}
                asio  websocket.cpp:449 operator()

                {"uri":"ws://127.0.0.1:8090"}
                th_a  websocket.cpp:678 connect
            root@NC-PH-1346-07:~/bitshares/issue25/bitshares-core# 


### Output - User error Case (1) 

            1857968ms th_a main.cpp:172 main ] wdata.ws_server: ws://localhost:8090
            0 exception: unspecified
            Underlying Transport Error
            {"message":"Underlying Transport Error"}
            asio websocket.cpp:449 operator()

            {"uri":"ws://localhost:8090"}
            th_a  websocket.cpp:678 connect

**Tip:**  Check if node running in localhost at port 8090.

### Output - User error Case (2)

- started the witness node using the command `./programs/witness_node/witness_node` and 
- configured the `witness_node_data_dir/config.ini - rpc-endpoint = server-ip:8090` also 
- changed the ips specified in main.cpp under `programs/cli_wallet` to server-ip and
- built the cli_wallet again using make.

            Logging RPC to file: logs/rpc/rpc.log
            323856ms th_a       main.cpp:136                  main                 ] key_to_wif( committee_private_key ): 5KCBDTcyDqzsqehcb52tW5nU6pXife6V2rX9Yf7c3saYSzbDZ5W 
            323856ms th_a       main.cpp:140                  main                 ] nathan_pub_key: BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV 
            323856ms th_a       main.cpp:141                  main                 ] key_to_wif( nathan_private_key ): 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3 
            Starting a new wallet with chain ID 4018d7844c78f6a6c41c6a552b898022310fc5dec06da467ee7905a8dad512c8 (from egenesis)
            `323862ms th_a       main.cpp:188                  main                 ] wdata.ws_server: ws://server-ip`
            0 exception: unspecified
            Invalid HTTP status.
                {"message":"Invalid HTTP status."}
                asio  websocket.cpp:449 operator()

                {"uri":"ws://server-ip"}
                th_a  websocket.cpp:678 connect


**Tip:** 

`./cli_wallet -s ws://server-ip` was used. Port is missing.  
If your node is running at port 8090, try this `./cli_wallet -s ws://server-ip:8090` (e.g. `./cli_wallet -s ws://127.0.0.1:8090`).

witness_node should be started with --rpc-endpoint parameter, otherwise you'll be unable to connect to it.

***

(ref)
- https://github.com/bitshares/bitshares-core/wiki/Wallet_Full-Nodes_Witness_Nodes
- https://github.com/bitshares/bitshares-core/issues/313

