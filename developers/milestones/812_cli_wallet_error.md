## cli_wallet_error 

- #812, #313

***

#### Issue:

I got the same exact error as in issue 1, issue 2, i started 
the witness node using the command `./programs/witness_node/witness_node` and 
configured the `witness_node_data_dir/config.ini - rpc-endpoint = server-ip:8090` also 
changed the main.cpp under `programs/cli_wallet` to `server-ip` and 
built the cli_wallet again using make and ran `./cli_wallet`. Still shows the exact error in the screenshot.

Also tried `./cli_wallet -s ws://server-ip` with the unmodified cli_wallet binary, still not working, I get this.

./cli_wallet -s ws://server-ip

    Logging RPC to file: logs/rpc/rpc.log
    323856ms th_a       main.cpp:136                  main                 ] key_to_wif( committee_private_key ): 5KCBDTcyDqzsqehcb52tW5nU6pXife6V2rX9Yf7c3saYSzbDZ5W 
    323856ms th_a       main.cpp:140                  main                 ] nathan_pub_key: BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV 
    323856ms th_a       main.cpp:141                  main                 ] key_to_wif( nathan_private_key ): 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3 
    Starting a new wallet with chain ID 4018d7844c78f6a6c41c6a552b898022310fc5dec06da467ee7905a8dad512c8 (from egenesis)
    323862ms th_a       main.cpp:188                  main                 ] wdata.ws_server: ws://server-ip 
    0 exception: unspecified
    Invalid HTTP status.
        {"message":"Invalid HTTP status."}
        asio  websocket.cpp:449 operator()

        {"uri":"ws://server-ip"}
        th_a  websocket.cpp:678 connect

***

#### Responses

-1-

from your own log: `323862ms th_a main.cpp:188 main ] wdata.ws_server: ws://server-ip`

so it seems you are using: `./cli_wallet -s ws://server-ip`

you need to use: `./cli_wallet -s ws://104.200.28.117:8090` assuming your node is running at port 8090.

-2-

- witness_node should be started with --rpc-endpoint parameter, otherwise you'll be unable to connect to it.
- you're connecting to ws://server-ip, which seems just an example and won't work as is. Try cli_wallet without any parameter.


-3-

Port is missing, should be ./cli_wallet -s ws://server-ip:8090

***


