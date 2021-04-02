
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

Let's get started, open a **new command line interface (CLI) window** and go to a directory you want to download the ``testnet`` branch files. And run the following command lines.  In this example, you create a ``bitshares-core-testnet`` directory.

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
     - ./programs/witness_node/
   * - cli_wallet 
     - ./programs/cli_wallet/

	 

Genesis File
~~~~~~~~~~~~~~~~

As you know, each blockchain starts with a genesis block that definitions are in a :ref:`genesis file <public-testnet-genesis-example>`. The bitshares-core-testnet has already prepared **a genesis.json file for the Public Testnet.**  Unless you want to adjust parameter values, you will not need to modify the public testnet genesis.json file.  

|

--------------

2. Configuration
----------------------------------------------------

2-1. Obtain a config.ini file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The BitShares Blockchain Data Configuration file exists in the blockchain data directory. And the data directory will be created by launching a witness_node. 

At this point, we just want to create a :ref:`configuration file (config.ini)  <bts-config-ini-eg-public-testnet>` to observe the parameters. If you want to use the Public Testnet, you can use the default configuration settings without changes.

Run the following command and end the process. You can use ``[ctrl]+c`` to end the process.

:: 
   
   ./programs/witness_node/witness_node
   
   
2-2. Blockchain Data Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As a default the BitShatres Blockchain data directory ``witness_node_data_dir`` will be created in a current directory.

For example, if your current directory is ``bitshares-core-testnet`` and you run this command ``./programs/witness_node/witness_node``, you will find the data directory in the same current directory with the programs folder.

::

  bitshares-core-testnet/
      + /programs/
        + /witness_node/
        + /cli_wallet/
        +....
      + /witness_node_data_dir/
        + /blockchain/
          + /database/
        + /logs/
        + /p2p/
        - [config.ini]
        - [logging.ini]
			  
2-3. Create own Data Directory (alternative)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to create own data directory, use ``--data-dir`` parameter and run the following command. This will create a **data** directory and a **testnet** folder inside of it.  

::

   ./programs/witness_node/witness_node --data-dir data/testnet

::

	bitshares-core-testnet/
		+ /programs/
			+ /witness_node/
			+ /cli_wallet/
			+....
		+ /data/
		   + /testnet/
			  + /blockchain/
				+ /database/
			  + /logs/
			  + /p2p/
			  - [config.ini]
			  - [logging.ini]

			  
			  
			  
2-4. Observe the config.ini file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Although you can use the default :ref:`config.ini <bts-config-ini-eg-public-testnet>` file for the Public Testnet, it's worth to recognize the parameters. 

In BitShares, a seed node is a node that accept incoming P2P connection. Its address is hard coded in the program, so when a new node starts, it will connect to the seed nodes by default. Every node (including seed nodes) tells the connected nodes where other nodes are, so all nodes can connect to each other.

.. list-table::
   :widths: 20 80
   :header-rows: 1
   
   * - 
     - Seed Node Information
   * - testnet
     - https://github.com/bitshares/bitshares-core/blob/testnet/libraries/egenesis/seed-nodes-testnet.txt
   * - production
     - https://github.com/bitshares/bitshares-core/blob/master/libraries/egenesis/seed-nodes.txt

|

----------------


3. Start the Testnet (witness_node)
--------------------------------------

Now you know the testnet directory structure and some of important files. Let's start the testnet! 

::

   ./programs/witness_node/witness_node 
   
   
If you want to use own data directory, do not forget to set the parameter ``--data-dir``! Otherwise, the default data directory ``witness_node_data_dir`` will be created to use in your current directory. 
   
::
   
   ./programs/witness_node/witness_node --data-dir data/testnet


   
If you want to use the ``cli_wallet``, you need to specify at least the rpc endpoint. For instance, 

::

    ./programs/witness_node/witness_node --rpc-endpoint "127.0.0.1:8090"

 
|

---------------

4.  Create a Testnet Account 
----------------------------------------------------

4-1. Preparation for CLI wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(If you have your testnet accounts already, you can jump this section.)

We want to use a BitShares Public UI wallet for testnet(https://test.xbts.io/) to register new testnet account. The public UI wallet has been set the faucet (https://testnet-faucet.xbts.io) already. The faucet address is used to pay the registration fee for new users! (*To learn more details, please refer BitShares Users Guide.*)

  1) Go to the Public testnet UI wallet: (https://test.xbts.io/) to create new testnet account.
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
     - 5KN-Test-ACTIVE-Private-key-Pn3jNVZBh
   * - **Memo**
     - public key
     - TEST5jork-Test-MEMO-Public-key-A91aWDZztyMCaR6
   * -  
     - private key
     - 5J3-Test-MEMO-Private-keyva7C9sYW6

	 
You might've noticed each public key start with **TEST**.  So, you know those private keys are for the testnet. If you create BitShares mainnet account, you will find **BTS** on the top of each private key.

**Note:** The Memo key is for decrypting transfer memos. 
  
|

-------------------
  
5.  Use of CLI wallet in Public Testnet
----------------------------------------------------

In this section, we will connect a ``cli_wallet`` and import an existing testnet account by importing the two private keys into your cli wallet. After we import the testnet account, we will test our first transaction ``transfer`` on the BitShares testnet blockchain.
 
.. Attention:: If you have newly created testnet account and just started a public testnet witness_node, you have to make sure if your node has been synced completely. Otherwise, you will not be able to find your new account data. 
 
  
5-1. Connect cli_wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's open new command window. If you have started the public testnet (``witness_node``) with the rpc endpoint (i.e.,"127.0.0.1:8090"), you will be able to connect your ``cli_wallet`` by the following command ::

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090

	
5-2. Set a password and Unlock the Cli Wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After successfully connected, it will prompt ``new >>>`` to set a password. 

* **For more detailed instructions, see the tutorial on** :ref:`How to Set a password and Unlock a Cli Wallet <cli-wallet-setpwd-unlock>`

   
After the ``unlock``, we can search and view the existing public testnet accounts data. Let's check if your testnet account is on the public testnet blockchain. We use the following command ``get_account`` to view an account information::
 
      unlocked >>> get_account ken-test01

   
And the following command ``list_account_balances`` to view the balance of the account:: 
  
       unlocked >>> list_account_balances ken-test01


.. note:: If you get errors and cannot find the testnet account, make sure your public testnet node has been synced completely.


If you could find your testnet account successfully, your next step is **importing** your testnet account into the cli wallet. 


5-3. Import your testnet account to a cli_wallet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^	
In the following section, we use *ken-test01* as a testnet user account to explain easier. You should replace the account by your testnet account. 


We want to import two private keys. First one is **Active Private key** to transfer your fund.    The next one is **Memo Private key** to transfer your memo data.   

      >>> import_key ken-test01 5KN-Test-ACTIVE-Private-key-Pn3jNVZBh   // Active private key
      >>> import_key ken-test01 5J3-Test-MEMO-Private-keyva7C9sYW6      // Memo private key

	
* ``import_key`` <name> "<wifkey>"

  - \<name\> is the account name owning the key
  - \<wifkey\> is the private key in WIF format
	

In BitShares Blockchain, balances are contained in accounts. Use the following as an example to import your testnet account balances. These balances can be claimed, with no fee.


      >>> import_balance ken-test01 ["5KN-Test-ACTIVE-Private-key-Pn3jNVZBh"] true

* ``import_balance`` <name> ["*"] true


After you imported your public testnet account and balances, let's check if you imported them successfully. If you have the public testnet account and balance in the current cli_wallet, it will show the data by the following command.

      >>> list_my_accounts


**Note:**
If you want to check the private key of the current cli_wallet, you can use a command ``get_private_key`` with the pair of the public key. 

	>>> get_private_key  TEST72uQ5-Test-ACTIVE-Public-key-6CKa3fpqrfye	
        5KN-Test-ACTIVE-Private-key-Pn3jNVZBh
	
	
	
5-4. Transfer funds
^^^^^^^^^^^^^^^^^^^^^^^^^

At this point, We should have your public testnet account into your cli wallet. Let's try to send some funds (testnet asset TEST) from ``ken-test01`` to ``faucet``.  If you know another public testnet account, you can sent a fund to the testnet account. Use the following command ::  	
	
    >>> transfer ken-test01 faucet 10 TEST "" true                     // without Memo
    >>> transfer ken-test01 faucet 25 TEST "Hi, send my TEST!" true    // with Memo



|
	  
----------
