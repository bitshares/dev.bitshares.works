
.. _public-testnet-guide:

***************
Public Testnet
***************

This section explains the process of customizing and deploying a graphene blockchain in the public testnet environment and also shows the steps to be a block producing node (witness node). 


.. contents:: Table of Contents
   :local:
   
-------

How to Set up Public Open Testnet 
======================================

1. Installation_Configuration 
----------------------------------------------------


1.1 BitShares-Core Testnet branch:: 

    git clone https://github.com/bitshares/bitshares-core.git bitshares-core-testnet
    cd bitshares-core-testnet/
    git checkout testnet


1.2 Configuration

**Blockchain Parameters**

The blockchain parameters can be modified in the
`libraries/chain/include/graphene/chain/config.hpp <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/config.hpp>`_ file::

    vim libraries/chain/include/graphene/chain/config.hpp

**Default Seed Node List**

We can add a default list of seed nodes that the witness is supposed to try to connect to in `libraries/app/application.cpp <https://github.com/bitshares/bitshares-core/blob/master/libraries/app/application.cpp>`_ and will add the IP/Address and port of the machine we are going to setup later already::

    testnet.bitshares.eu:11010

The full change set can be seen in the corresponding `git commit <https://github.com/BitSharesEurope/graphene-testnet/commit/94f0a95be6f80cb5a7926ba6cc920dd795eb3a19>`_

1.3 Initial Compilation

 ::

   cmake .
    make

We first need to compile the graphene toolkit so that we can let it generate a plain genesis file in the proper format.



2. Genesis Configuration
----------------------------------------------------

2.1 Create a Genesis file

We will create a genesis file named ``my-genesis.json`` that contains the genesis block::

    mkdir -p genesis
    programs/witness_node/witness_node --create-genesis-json genesis/my-genesis.json
    vim genesis/my-genesis.json

The ``my-genesis.json`` is the initial state of the network.

2.2 Edit a Genesis file

If you want to customize the network's initial state, edit ``my-genesis.json``.
This allows you to control things such as:

- The accounts that exist at genesis, their names and public keys
- Assets and their initial distribution (including core asset)
- The initial values of chain parameters
- The account / signing keys of the ``init`` witnesses (or in fact any account at all).

The chain ID is a hash of the genesis state.  All transaction signatures are only valid for a single chain ID.  So editing the genesis file will change your chain ID, and make you unable to sync with all existing chains (unless one of them has exactly the same genesis file you do).

**Copy final Genesis**

We now copy our gensis template file over to the graphene root directory::

    $ cp genesis/my-genesis.json genesis.json
    $ vim genesis.json
    $ git add genesis.json
    $ git commit -m "Added genesis.json"

The ``initial timestamp`` needs to be pasted into ``genesis.json`` file in the `initial_timestamp` field. Choose it relatively close to the future where you can generate the genesis block (e.g., now plus 10 minutes).

**Include Genesis into the binaries**

To let the binaries know about your new genesis block, we need to recompile it and provide ``cmake`` with the parameter to identify the genesis block properly::

    $ make clean
    $ find . -name "CMakeCache.txt" | xargs rm -f
    $ find . -name "CMakeFiles" | xargs rm -Rf
    $ cmake -DGRAPHENE_EGENESIS_JSON="$(pwd)/genesis.json" .

You can add the ``GRAPHENE_EGENESIS_JSON`` to the default parameters by adding::

    set(GRAPHENE_EGENESIS_JSON "${CMAKE_CURRENT_SOURCE_DIR}/genesis.json" )

to the ``CMakeLists.txt`` file. This way, you don't need to provide this parameter all the time.



3. Initialize Blockchain
----------------------------------------------------

3.1 Initialize the Genesis Block

The ``--enable-stale-production`` flag tells the ``witness_node`` to produce on a chain with zero blocks or very old blocks.  We specify the ``--enable-stale-production`` parameter on the command line as we will not normally need it (although it can also be specified in the config file).::

    programs/witness_node/witness_node --genesis-json genesis.json \
                                      --enable-stale-production \
                                      --data-dir data/testnet

**Chain ID:**::

    Started witness node on a chain with 0 blocks.
    Chain ID is cf307110d029cb882d126bf0488dc4864772f68d9888d86b458d16e6c36aa74b

.. Note:: If other witness produces blocks and witness participation is high enough, subsequent runs which connect to an existing witness node over the p2p network, or which get blockchain state from an existing data directory, need not have the ``--enable-stale-production`` flag.

3.2 Set up Block Production

Create a very basic configuration file in ``data/testnet/config.ini``::
    
    $ mkdir -p data/testnet
    $ vim data/testnet/config.ini

All we put into the configuration file is the ids and the keys for the witnesses so that we can start producing blocks ::

    witness-id = "1.6.1"
    witness-id = "1.6.2"
    witness-id = "1.6.3"
    witness-id = "1.6.4"
    witness-id = "1.6.5"
    witness-id = "1.6.6"
    witness-id = "1.6.7"
    witness-id = "1.6.8"
    witness-id = "1.6.9"
    witness-id = "1.6.10"
    # For each witness, add pubkey and private key:
    private-key = ["GPH6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]
    private-key = [<pubkey>,<privkey>]

This authorizes the ``witness_node`` to produce blocks on behalf of the listed ``witness-id``'s, and specifies the private key needed to sign those blocks.  Normally each witness would be on a different node, but for the purposes of this testnet, we will start out with all witnesses signing blocks on a single node.

.. Note:: The setting ``rpc-endpoint = 0.0.0.0:11011`` will open up the RPC-port ``11011`` to connect a cli-wallet or web wallet to it. With the ``p2p-endpoint = 0.0.0.0:11010`` being accessible from the internet, this node can be used as seed node.

3.3 Embed the Genesis block (optional)

Now that we have the blockchain established and the used correct genesis block, we can have it embedded into the binaries directly. For that reasons we have moved it into the root directory and called it ``genesis.json`` for the default compile toolchain to catch it automatically. We recompile to include the genesis block with::

    make clean
    find . -name "CMakeCache.txt" | xargs rm -f
    find . -name "CMakeFiles" | xargs rm -Rf
    cmake -DCMAKE_BUILD_TYPE=Release .

Deleting caches will reset all ``cmake`` variables, you might be asked to set other ``cmake`` variables. You will have to add those variables to the ``cmake`` line above.

Embedding the genesis copies the entire content of genesis block into the ``witness_node`` binary, and additionally copies the chain ID into the ``cli_wallet`` binary.  Embedded genesis allows the following simplifications to the subsequent instructions:

* You need **not** specify the genesis file on the witness node command line, or in the witness node configuration file.
* You need **not** specify the chain ID on the ``cli_wallet`` command line when starting a new wallet.


4. Connect a CLI wallet
----------------------------------------------------


We will show how to connect a cli-wallet to the new blockchain and generate our first transaction on the new blockchain.

In order to create a wallet, you must specify the previously setup server. With the witness node’s default access control settings.

**Create a Wallet - Public Testnet**::

    programs/cli_wallet/cli_wallet --wallet-file my-wallet.json -s ws://127.0.0.1:11011 -H 127.0.0.1:8090 -r 127.0.0.1:8099

> Note: The parameter ``-H`` is required so that we can interface with the cli-wallet via RPC-HTTP-JSON, later while ``-r`` will open a port for the Ruby-based faucet.

If you get the `set_password` prompt, it means your CLI wallet has successfully connected to the testnet witness node.

* ``set_password``

This password is used to encrypt the private keys in the wallet. (e.g., `supersecret` is a password)::

    >>> set_password supersecret

* ``unlock`` the wallet::

    >>> unlock supersecret


Gain Access to Stake
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In Graphene, balances are contained in accounts. To import an account that exists in the Graphene genesis. (i.g.,\<name\> is an account name owning the key.  \<wifkey\> is a private key)

* ``import_key``::

    >>> import_key <name> "<wifkey>"

Funds are stored in genesis balance objects. These funds can be claimed, with no fee.

* ``import_balance``::

    >>> import_balance <name> ["*"] true

Manage Accounts
^^^^^^^^^^^^^^^^^^^^^

* ``upgrade_account``

 **Obtain Lifetime member (LTM) status**: Requires lifetime member (LTM) status to create an account. You can upgrade the account.::

    >>> upgrade_account faucet true

* ``register_account``::

    register_account <name> <owner-public_key> <active-public_key> <registrar_account> <referrer_account> <referrer_percent> <broadcast>

 This command allows you to register an account using only a **public key**. 

 **example**::

    >>> register_account alpha GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF faucet faucet 0 true

 Test ``transfer`` to send `CORE` from `faucet` to `alpha` user.::

    >>> transfer faucet alpha 100000 CORE "here is the cash" true

**Open a new Wallet for `alpha` user**::

    >>> import_key alpha 5HuCDiMeESd86xrRvTbexLjkVg2BEoKrb7BAA5RLgXizkgV3shs

    >>> upgrade_account alpha true

    >>> create_witness alpha "http://www.alpha" true

**Obtain the private key**

The ``get_private_key`` command allows us to obtain the **private key** corresponding to the block signing key.::

    >>> get_private_key GPH6viEhYCQr8xKP3Vj8wfHh6WfZeJK7H9uhLPDYWLGCRSj5kHQZM


5. Establish a Committee
----------------------------------------------------


Our network, of course, needs a committee. We need to initially create new accounts and let them apply as committee member.

5.1 Creating members

* ``create_account_with_brain_key``

::

    create_account_with_brain_key com0 com0 faucet faucet true
    create_account_with_brain_key com1 com1 faucet faucet true
    create_account_with_brain_key com2 com2 faucet faucet true
    create_account_with_brain_key com3 com3 faucet faucet true
    create_account_with_brain_key com4 com4 faucet faucet true
    create_account_with_brain_key com5 com5 faucet faucet true
    create_account_with_brain_key com6 com6 faucet faucet true

5.2 Upgrading members

Since only lifetime members can be committee members, we need to fund these accounts ``transfer``  and upgrade ``upgrade_account`` them accordingly:

::

    transfer faucet com0 100000 CORE "some cash" true
    transfer faucet com1 100000 CORE "some cash" true
    transfer faucet com2 100000 CORE "some cash" true
    transfer faucet com3 100000 CORE "some cash" true
    transfer faucet com4 100000 CORE "some cash" true
    transfer faucet com5 100000 CORE "some cash" true
    transfer faucet com6 100000 CORE "some cash" true
    upgrade_account com0 true
    upgrade_account com1 true
    upgrade_account com2 true
    upgrade_account com3 true
    upgrade_account com4 true
    upgrade_account com5 true
    upgrade_account com6 true

5.3 Registering as committee member

We can apply for committee with create_committee_member:

* ``create_committee_member``

 ::

    create_committee_member com0 "" true
    create_committee_member com1 "" true
    create_committee_member com2 "" true
    create_committee_member com3 "" true
    create_committee_member com4 "" true
    create_committee_member com5 "" true
    create_committee_member com6 "" true


5.4 Voting with faucet account

All we need to do know is vote for our own committee members:

* ``vote_for_committee_member``

::

    vote_for_committee_member faucet com0 true true
    vote_for_committee_member faucet com1 true true
    vote_for_committee_member faucet com2 true true
    vote_for_committee_member faucet com3 true true
    vote_for_committee_member faucet com4 true true
    vote_for_committee_member faucet com5 true true
    vote_for_committee_member faucet com6 true true

	
6. Web Wallet
----------------------------------------------------


Since we need to provide a way for people to enter the network/blockchain, we need to install the web wallet into nginx.

6.1 Installation of Dependencies::

    sudo apt-get install git nodejs-legacy npm
    sudo npm install -g webpack coffee-script

6.2 Fetching the web wallet

Afterwards, we download the bitshares-ui repository from Cryptonomex and install the Node dependencies::

    git clone https://github.com/bitshares/bitshares-ui
    cd bitshares-ui/

    for I in dl web; do cd $I; npm install; cd ..; done

6.3 Configuration

Obtain the chain_id of the chain we are running.::

    $ curl --data '{"jsonrpc": "2.0", "method": "get_chain_properties", "params": [], "id": 1}' http://127.0.0.1:11011/rpc && echo

The chain id is used to let the web wallet know to which network it connects and how to deal with it. For this we modify the file dl/src/chain/config.coffee and add our blockchain::

    Test:
    core_asset: "TEST"
    address_prefix: "TEST"
    chain_id: "<chain-id>"

Furthermore, we need to tell our web wallet to which witness node to connect to. This can be done in the file dl/src/stores/SettingsStore.js.::

    connection: "ws://<host>/ws",
    faucet_address: "https://<host>",

    # also edit the "default" settings

6.4 Compilation

**Compile the web wallet**  

- This will generate the static files in the dist/ folder.

::

    cd web
    npm run build


|

|
