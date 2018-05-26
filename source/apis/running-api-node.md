## Running a BitShares API Node

#### Contents:
- [Introduction](/source/apis/running-api-node.md#introduction)
- [Running a Basic API Node](/source/apis/running-api-node.md#running-a-basic-api-node)
- [Running a Production API Node](/source/apis/running-api-node.md#running-a-production-api-node)
    - GNU screen
    - Docker

****

## Introduction

An Bitshares API Node is a `witness_node` instance specially configured to serve applications with data endpoints. Any bitshares application(gateway, explorer, wallet, trading program, etc) interacts with the decentralized network(blockchain) by connecting to one or many api nodes.

If you plan to run a business on top of bitshares you will probably want one or several api nodes of your property. You can use public nodes but they are very busy and most of the time running old versions, the most api nodes around the better for the network and final users.

This tutorial is for users that already have `witness_node` compiled successfully.

## Running a Basic API Node

We have witness_node executable and we know we can run it by terminal. First thing is to check the options available:

    ./programs/witness_node/witness_node --help

It is important to know what version you are using to know what to expect from a node. Get the version of the node by using the `--version` flag:

    ./programs/witness_node/witness_node --version

The basic api node will run by:

    ./programs/witness_node/witness_node --rpc-endpoint "127.0.0.1:8090"

With the above command you are starting a node that will listen for api calls at port 8090, only at localhost. This can be changed to listen in the internet, local area network or any IP address you own.

By default, the blockchain directory will be `witness_node_data_dir` unless you specify a data directory, for example:

    ./programs/witness_node/witness_node --rpc-endpoint "127.0.0.1:8090" --data-dir data/my-blockprod

Depending on your hardware and use purposes of your api node, you will start it with some custom parameters, enabling and disabling plugins. This will be explained as we go.

Options to the witness_node binary can be added to the config file or passed by command line as we did above. Check the default `config.ini` created when started the node by the first time to make an idea on how you can customize it.

A typical node start command can be something like:

    programs/witness_node/witness_node --data-dir data/my-blockprod --rpc-endpoint "127.0.0.1:8090" --max-ops-per-account 10000 --partial-operations true

Above we limit the operations per account to 1000 to save RAM. This is the default behaviour for newest versions of witness_node as a full node needs more than 80 gigs of ram to run.

We call full node to an api node that haves all the account history from all the accounts in the bitshares blockchain. As the amount of RAM needed is so big and increasing this kind of nodes are each time more rare.

Look in the terminal for messages like:

    1256186ms th_a       application.cpp:499           handle_block         ] Got block: #10000 time: 2015-10-13T23:15:42 latency: 73184714186 ms from: cyrano  irreversible: 9976 (-24)
    1267475ms th_a       application.cpp:499           handle_block         ] Got block: #20000 time: 2015-10-14T07:37:33 latency: 73154614475 ms from: bitcube  irreversible: 19975 (-25)

If you see this, the blockchain is synchronizing ok. Complete the sync is a long process that depends a lot in your hardware. While you see the blocks coming in the witness node terminal you are in good track.

***

## Running a Production API Node

### GNU screen

This is the easier way, start a screen terminal and run your node on it. See: [Manage a running Node in the background by using GNU screen](/source/references_tools/manage_node_gun_screen.md#manage-a-running-node-in-the-background-by-using-gnu-screen)

This works but it is not the most recommended, in a reboot the node admin will need to run everything again.

### Docker

#### docker-compose.yaml:

    version: '3'
    services:

     fullnode:
      # Image to run
      image: fb3d7a273ce4
      # Optional environmental variables
      # environment:
      ports:
       - "0.0.0.0:8090:8090"
      volumes:
      - "bitshares-fullnode:/var/lib/bitshares"

     delayed_node:
      # Image to run
      image: fb3d7a273ce4
      # Optional environmental variables
      environment:
       - 'BITSHARESD_PLUGINS=delayed_node witness'
       - 'BITSHARESD_TRUSTED_NODE=ws://fullnode:8090'
      ports:
       - "0.0.0.0:8091:8090"
      volumes:
      - "bitshares-fullnode:/var/lib/bitshares"
        #  links: 
        #- fullnode

    volumes:
     bitshares-fullnode:

then

    docker-compose up



***

(ref)
- https://github.com/oxarbitrage/worker-proposals/blob/master/bitshares_api_node.md
- https://github.com/bitshares/bitshares-core/wiki/Manage-your-nodes-by-using-gnu-screen
