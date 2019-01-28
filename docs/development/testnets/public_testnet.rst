
.. _public-testnet-guide:

************************
Public Testnet Set up
************************

In this section, we are going to set up the BitShares Public Testnet. For the Public Testnet, you should download a testnet branch from the BitShares-Core Github repository. The testnet branch files are for the Testnet. 


.. contents:: Table of Contents
   :local:
   
-------


Depending on your OS, we have the Installation guides available. To see more Installation options: Go to :ref:`Installation Guide <installation-guide>`.


1. Installation
----------------------

1-1. Download the Source files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's get started, open a **new command line interface (CLI) window** and go to a directory you want to download the ``testnet`` branch files. And run the following command lines. 

 ::
 
    git clone https://github.com/bitshares/bitshares-core.git bitshares-core-testnet
    cd bitshares-core-testnet/
    git checkout testnet
    git submodule update --init --recursive


1-2. Initial Compilation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After you download a testnet branch files in a ``bitshares-core-testnet`` directory, let's build the programs. Run the following commands. This command will create two program files (witness_node and cli_wallet). 

 ::

   cmake .
    make

You will find the compiled program files in the below folders. 

.. list-table::
   :widths: 20 80
   :header-rows: 1
   
   * - program name
     - directory and folder
   * - witness_node 
     - ../bitshares-core-testnet/programs/witness_node/
   * - cli_wallet 
     - ../bitshares-core-testnet/programs/cli_wallet/

	 

Genesis File
~~~~~~~~~~~~~~~~

As you know, each blockchain starts with a genesis block that definitions are in a :ref:`genesis file <public-testnet-genesis-example>`. **The bitshares-core-testnet has already prepared a genesis.json file for the Public Testnet.**  You will not need to modify the genesis.json file. You will find the ``genesis.json`` in the **bitshares-core-testnet** directory.

|

--------------

2. Configuration
----------------------------------------------------

2-1. Obtain a config.ini file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The BitShares Blockchain Data Configuration file exists in the blockchain data directory. And the data directory will be created by launching a witness_node. 

At this point, we just want to create the configuration file ``config.ini`` to observe the parameters. If you want to use the Public Testnet, you can use the default configuration settings without changes.

Run the following command and end the process. In Windows, you can use ``[ctl]+c`` to end the process.

:: 
   
   ./programs/witness_node/witness_node
   
   
2-2. Blockchain Data Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As a default the BitShatres Blockchain data directory ``witness_node_data_dir`` will be created in a current directory.

For example, if your current directory is ``bitshares-core-testnet`` and you run this command ``./programs/witness_node/witness_node``, you will find the data directory in the same current directory with the programs folder.

::

	../bitshares-core-testnet/
		+ [programs]
			+ [witness_node]
			+ [cli_wallet]
			+....
		+ [witness_node_data_dir]
			  + [blockchain]
				+ [database]
			  + [logs]
			  + [p2p]
			  + config.ini
			  + logging.ini

			  
2-3. Create own Data Directory (alternative)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to create own data directory, use ``--data-dir`` parameter and run the following command. This will create a **data** directory and a **testnet** folder inside of it.  

::

   ./programs/witness_node/witness_node --data-dir data/testnet

::

	../bitshares-core-testnet/
		+ [programs]
			+ [witness_node]
			+ [cli_wallet]
			+....
		+ [data]
		   + [testnet]
			  + [blockchain]
				+ [database]
			  + [logs]
			  + [p2p]
			  + config.ini
			  + logging.ini

			  
			  
			  
2-4. Observe the config.ini file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Although you can use the default :ref:`cinfig.ini <bts-config-ini-eg-public-testnet>` file for the Public Testnet, it's worth to recognize the parameters. 

In BitShares, a seed node is a node that accept incoming P2P connection. Its address is hard coded in the program, so when a new node starts, it will connect to the seed nodes by default. Every node (including seed nodes) tells the connected nodes where other nodes are, so all nodes can connect to each other.

.. list-table::
   :widths: 20 80
   :header-rows: 1
   
   * - 
     - Seed Node Information
   * - testnet
     - https://github.com/bitshares/bitshares-core/blob/testnet/libraries/app/application.cpp
   * - production
     - https://github.com/bitshares/bitshares-core/blob/master/libraries/app/application.cpp#L168-L187	 

|

----------------


3. Start the Testnet (witness_node)
--------------------------------------

Now you know the testnet directory structure and some of important files. Let's start the testnet! 

::

   ./programs/witness_node/witness_node 
   
   
If you want to use own data directory, do not forget to set the parameter! Otherwise, the default data directory `witness_node_data_dir` will be created to use in your current directory. 
   
::
   
   ./programs/witness_node/witness_node --data-dir data/testnet


   
If you want to use the ``cli_wallet``, you need to specify at least the rpc endpoint. For instance, 

::

    ./programs/witness_node/witness_node --rpc-endpoint "127.0.0.1:8090"

 
									  
3-1. Set up Block Production (Option)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to produce blocks, you need to modify the data configulation file ``config.ini``. 

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

This authorizes the ``witness_node`` to produce blocks on behalf of the listed **witness-id's**, and specifies the private key needed to sign those blocks.  Normally each witness would be on a different node, but for the purposes of this testnet, we will start out with all witnesses signing blocks on a single node.

|

---------------

4.  Preparation 
----------------------------------------------------

4-1. Create a testnet account 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(If you have your testnet accounts already, you can jump this section.)

We want to use a BitShares Public UI wallet for testnet(http://testnet.bitshares.eu/) to register new testnet account. The public UI wallet has been set the faucet (https://faucet.testnet.bitshares.eu/) already. The faucet address is used to pay the registration fee for new users! (*To learn more details, please refer BitShares Users Guide.*)

  1) Go to the Public testnet UI wallet: (http://testnet.bitshares.eu/) to create new testnet account.
  2) If you see "Application initialization issues", try to select "Public Testnet Server (...)" from a FULL NODE API SERVER dropdown list. 
  3) Click a [CREATE ACCOUNT] button to register a new testnet account. 
  4) Save your password and Create an account.
  5) If the new account was created successfully, you will receive some asset TEST for the Public Testnet transactions.
  
4-2. Obtain your wallet key pairs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  
  6) Go to a [Permissions] page and save your new UI wallet Active, Owner, and Memo key pairs (public key and private key).
  7) Keep your private key information securely.

Example Key Pairs (testnet account: ken-test01) 
~~~~~~~~~~~~~~~~~
The below table is Example Owner, Active, and Memo key pairs. Each authority has a public key and a private key. The private key is to be kept securely since it gives you control over a wallet.


.. list-table::
   :widths: 15 10 60
   :header-rows: 1

   * - permissions
     - keys
     - values	 
   * - **Owner**
     - public key
     - TEST5qqct-Test-OWNER-Public-key-jqXfSopvhW5A
   * - 
     - private key
     - 5JRf-Test-OWNER-Private-key-ERGw9zctF8kB
   * - **Active**
     - public key
     - TEST72uQ5-Test-ACTIVE-Public-key-6CKa3fpqrfye
   * -  
     - private key
     - P5KN-Test-ACTIVE-Private-key-Pn3jNVZBh
   * - **Memo**
     - public key
     - TEST5jork-Test-MEMO-Public-key-A91aWDZztyMCaR6
   * -  
     - private key
     - P5J3-Test-MEMO-Private-keyva7C9sYW6

	 
You might've noticed each public key start with **TEST**.  So, you know those private keys are for the testnets. If you create BitShares mainnet account, you will find **BTS** on the top of each private key.
  
|

-------------------
  
5.  Use of CLI wallet in public testnet
----------------------------------------------------

In this section, we will connect a ``cli_wallet`` and import an existing testnet account by importing the two private keys into your cli wallet. After we import the testnet account, we will test our first transaction ``transfer`` on the BitShares testnet blockchain.
 
.. Attention:: If you have newly created testnet account and just started a public testnet witness_node, you have to make sure if your node has been synced completely. Otherwise, you will not be able to find your new account data. 
 
  
5-1. Connect cli_wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's open new command window. If you have started the public testnet (``witness_node``) with the rpc endpoint (i.e.,"127.0.0.1:8090"), you will be able to connect your ``cli_wallet`` by the following command ::

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090

	
5-2. Set a password 
^^^^^^^^^^^^^^^^^^^^^^^

If you are first time connecting the ``cli_wallet``, you will receive a ``set_password`` prompt. That means your cli wallet has been connected successfully to the public testnet witness_node.

We are going to set a password and unlock the ``cli_wallet``. The password is used to encrypt the private keys in the wallet. In the below,*supersecret* is a password. 

* ``set_password`` 

   >>> set_password supersecret

* ``unlock`` the wallet::

   >>> unlock supersecret

   
5-3. Import your testnet account to a cli_wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^	
In the following section, we use *ken-test01* as a testnet user account to explain easier. You should replace the account by your testnet account. 

After the ``unlock``, we can find and view the existing public testnet accounts data. Let's check if your testnet account is on the public testnet blockchain. We use the following command to view an account information::
 
      unlocked >>> get_account ken-test01

   
And the following command to view the balance of the account:: 
  
       unlocked >>> list_account_balances ken-test01


.. note:: If you get errors and cannot find the testnet account, make sure your public testnet node has been synced completely.


If you could find your testnet account successfully, your next step is **importing** your testnet account into the cli wallet. 

We want to import two private keys. First one is **Active Private key** to transfer your fund.    The next one is **Memo Private key** to transfer your memo data.   

      >>> import_key ken-test01 P5KN-Test-ACTIVE-Private-key-Pn3jNVZBh   // Active private key
      >>> import_key ken-test01 P5J3-Test-MEMO-Private-keyva7C9sYW6      // Memo private key

	
* ``import_key`` <name> "<wifkey>"

  - \<name\> is the account name owning the key
  - \<wifkey\> is the private key in WIF format
	

In BitShares Blockchain, balances are contained in accounts. Use the following as an example to import your testnet account balances. These balances can be claimed, with no fee.


      >>> import_balance ken-test01 ["P5KN-Test-ACTIVE-Private-key-Pn3jNVZBh"] true

* ``import_balance`` <name> ["*"] true


After you imported your public testnet account and balances, let's check if you imported them successfully. If you have the public testnet account and balance in the current cli_wallet, it will show the data by the following command.

      >>> list_my_accounts


**Note:**
If you want to check the private key of the current cli_wallet, you can use a command ``get_private_key`` with the pair of the public key. 

	>>> get_private_key  TEST72uQ5-Test-ACTIVE-Public-key-6CKa3fpqrfye	
        P5KN-Test-ACTIVE-Private-key-Pn3jNVZBh
	
	
	
5-4. Transfer funds
^^^^^^^^^^^^^^^^^^^^^^^^^

At this point, We should have your public testnet account into your cli wallet. Let's try to send some funds (testnet asset TEST) from ``ken-test01`` to ``faucet``.  If you know another public testnet account, you can sent a fund to the testnet account. Use the following command ::  	
	
    >>> transfer ken-test01 faucet 10 TEST "" true                     // without Memo
    >>> transfer ken-test01 faucet 25 TEST "Hi, send my TEST!" true    // with Memo



|
	  
----------

6. Establish a Committee (Option - Block Production)
----------------------------------------------------


Our network needs a committee. We need to initially create new accounts and let them apply as committee member.

6-1. Creating members
^^^^^^^^^^^^^^^^^^^^^^

* ``create_account_with_brain_key``

::

    create_account_with_brain_key com0 com0 faucet faucet true
    create_account_with_brain_key com1 com1 faucet faucet true
    create_account_with_brain_key com2 com2 faucet faucet true
    create_account_with_brain_key com3 com3 faucet faucet true
    create_account_with_brain_key com4 com4 faucet faucet true
    create_account_with_brain_key com5 com5 faucet faucet true
    create_account_with_brain_key com6 com6 faucet faucet true

6.2 Upgrading members
^^^^^^^^^^^^^^^^^^^^^^^^^

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

	
6.3 Registering as committee member
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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


6.4 Voting with faucet account
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

-------------
	


|

|
