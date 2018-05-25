
Blockchain Interaction
==========================

To interface your existing platform with BitShares, you can make use of
:ref:`rpc` and :ref:`websocket-calls` to either a :ref:`full-node` (for monitoring only) or the :ref:`cli-wallet-launching` (for accessing funds). 

All API calls are formated in JSON and return JSON only. You can read more about
the API in the separated :doc:`API documentation </api/index>`.

The Graphene toolkit comprises several tools that allow interaction with the
blockchain on different levels and shall thus be briefly described in the
following sections.

|

.. _full-node:

Full Node
----------------

We here distringuish between full nodes (a.k.a. *non-block producing* witness
nodes) and *block producing* witness nodes. Both are implemented by the same
executable but the latter requires some additional parameters to be defined and
the corresponding witness voted active by the shareholders.


Both represent nodes in the network that verify all transactions and blocks
against the current state of the overall network. Hence, we recommend all
service providers to run an maintain their own **full nodes** for reliability
and security reasons.

Full Nodes
^^^^^^^^^^^^^

The full node is launched according to:

.. code-block:: sh

    ./programs/witness_node/witness_node

It takes an optional `--data-dir` parameter to define a working and data
directory to store the configuration, blockchain and local databases (defaults
to ``witness_node_data_dir``. Those will be automatically created with default
settings if they don't exist locally set.

Configuration
^^^^^^^^^^^^^^^^^^

The configuration file ``config.ini`` in your data directory is commented and
contains the following essential settings:

* ``p2p-endpoint``
    Endpoint for P2P node to listen on
* ``seed-node``
    P2P nodes to connect to on startup (may specify multiple times)
* ``checkpoint``
    Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints.
* ``rpc-endpoint``
    Endpoint for websocket RPC to listen on (e.g. ``0.0.0.0:8090``)
* ``rpc-tls-endpoint``
    Endpoint for TLS websocket RPC to listen on
* ``server-pem``
    The TLS certificate file for this server
* ``server-pem-password``
    Password for this certificate
* ``genesis-json``
    File to read Genesis State from
* ``api-access``
    JSON file specifying API permissions
* ``enable-stale-production``
    Enable block production, even if the chain is stale. (unless for private testnets should be ``false``)
* ``required-participation``
    Percent of witnesses (0-99) that must be participating in order to produce blocks
* ``allow-consecutive``
    Allow block production, even if the last block was produced by the same witness.
* ``witness-id``
    ID of witness controlled by this node (e.g. "1.6.5", quotes are required, may specify multiple times)
* ``private-key``
    Tuple of ``[PublicKey, WIF private key]`` (may specify multiple times)
* ``track-account``
    Account ID to track history for (may specify multiple times)
* ``bucket-size``
    Track market history by grouping orders into buckets of equal size measured
    in seconds specified as a JSON array of numbers
* ``history-per-size``
    How far back in time to track history for each bucket size, measured in the number of buckets (default: 1000)

.. note:: Folders and files are considered to be relative to the working
          directory (i.e. the directory from which the executables are launched from)

Enabling Remote Procedure Calls (RPC)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to allow RPC calls for blockchain operations you need to modify the
following entry in the configuration file:::

    rpc-endpoint = 0.0.0.0:8090

This will open the port 8090 for global queries only. Since the witness node
only maintains the blockchain and (unless you are an actively block producing
witness) no private keys are involved, it is safe to expose your witness to the
internet.

Restarting the witness node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When restarting the witness node, it may be required to append the
`--replay-blockchain` parameter to regenerate the local (in-memory) blockchain
state.

Enabling Block Production
^^^^^^^^^^^^^^^^^^^^^^^^

For block production, the required parameters to be defined are 

* ``witness-id`` and
* ``private-key`` as a pair of public key and wif private key.

The witness-id and public key can be obtain via:::

    >>> get_witness <accountname>
    {
      [...]
      "id": "1.6.10",
      "signing_key": "GPH7vQ7GmRSJfDHxKdBmWMeDMFENpmHWKn99J457BNApiX1T5TNM8",
      [...]
    }

Assuming we want to maintain the witness with id ``1.6.10``, the corresponding
setting would be:::

    witness-id = "1.6.10"

The required private keys can be exported from most wallets (e.g.
``dump_private_keys``) for configuration according to:::

    private-key = ["BTS7vQ7GmRSJfDHxKdBmWMeDMFENpmHWKn99J457BNApiX1T5TNM8","5JGi7DM7J8fSTizZ4D9roNgd8dUc5pirUe9taxYCUUsnvQ4zCaQ"]
	
|
	

.. _delayed-full-node:

Delayed Full Node
-------------------

The delayed full node node will provide us with a delayed and several times
confirmed and verified blockchain. Even though DPOS is more resistant against
forks than most other blockchain consensus schemes, we delay the blockchain here
to reduces the risk of forks even more. In the end, the delayed full node is
supposed to never enter an invalid fork.

The delayed full node will need the IP address and port of the p2p-endpoint
from the trusted full node and the number of blocks that should be delayed.  We
also need to open the RPC/Websocket port (to the local network!) so that we can
interface using RPC-JSON calls.

For our example and for 10 blocks delaye (i.e. 30 seconds for 3 second block
intervals), we need:

.. code-block:: sh

    ./programs/delayed_node/delayed_node --trusted-node="192.168.0.100:8090" \
                                         --delay-block-count=10 \
                                         --rpc-endpoint="192.168.0.101:8090"
	

|

.. _cli-wallet-launching:

CLI Wallet
----------------

The following will explain how to use the console wallet (not GUI).

Launching
^^^^^^^^^^^^

The `cli_wallet` creates a local `wallet.json` file that contains the encrypted
private keys required to access the funds in your account. It **requires** a
running witness node (not necessarily locally) and connects to it on launch:

.. code-block:: sh

    programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090

Depending on the actual chain that you want to connect to your may need to
specifiy `--chain-id`.

Enabling Remote Procedure Calls (RPC)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to allow RPC calls for wallet operations (spend, buy, sell, ...) you
can choose between pure RPC or RPC-HTTP requests. In this tutorial, the latter
is prefered since well established libraries make use of the RPC-HTTP protocol.

The cli-wallet can open a RPC port so that you can interface your
application with it. You have the choices of
* websocket RPC via the ``-r`` parameter, and
* HTTP RPC via the ``-H`` parameter:

To enable RPC-HTTP in your wallet you need to run:

.. code-block:: sh

    # recommended for use with python, or curl:
    programs/cli_wallet/cli_wallet --rpc-http-endpoint="127.0.0.1:8092"
    # or
    programs/cli_wallet/cli_wallet --rpc-endpoint="127.0.0.1:8092"

depending on the kind of RPC protocol.

This will open the port 8092 for local queries only. It is not recommended to
publicly expose your wallet!

A tutorial for the BitShares cli-wallet can be found in the
:doc:`BitShares tutorials <../bitshares/tutorials/cli-wallet-usage>`.





Web Wallet
-------------

The web wallet is a wallet implemented solely in Javascript. It makes use of
modern Web development tools and libraries -- to just a few:

* Coffee-Script
* LESS
* React-JS
* WebPack
* LoDash
* Foundation
* Highcharts
* Mocha
* ...

The webwallet (per default) connects to a full node (non-block-producing witness
node) on the same host via websockets.

Download
^^^^^^^^^^^

The sources can be downloaded from github.

.. code-block:: sh

    git clone https://github.com/cryptonomex/graphene-ui

They consist of libraries, a JS-console, and the wallet, as well as other
tools.

Installing Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^

First, we need to install the dependencies via `npm`:

.. code-block:: sh

    for I in cli dl web; do cd $I; npm install; cd ..; done

Bundling
^^^^^^^^^^

We now bundle the web wallet into JavaScript, CSS, and HTML assets.

.. code-block:: sh

    cd ./web
    npm run build

The resulting assets can be found in the `dist` folder.

Testing Bundle
^^^^^^^^^^^^^^^^^^

.. code-block:: sh

    npm test

Live Development
^^^^^^^^^^^^^^^^^

.. code-block:: sh

    npm start

	
-----

*Frequestly Asked Questions*

* :ref:`CLI Wallet FAQ <cli-faq>`
* :ref:`Witness FAQ <witness-faq>`

|


