
.. _private-testnet-guide:

**************************************
Set up BitShares Private Testnet 
**************************************


This chapter provides for instructions for preparing BitShares Private Testnet. 

.. contents:: Table of Contents
   :local:
   
-------

In this chapter, we define our base directory ``$HOME/`` and create two directories inside of $HOME directory. 

.. list-table::
   :widths: 20 80
   :header-rows: 0
   
   * - **$HOME/bitshares-testnet**
     - uses for downloading BitShares Core files and build the programs
   * - **$HOME/Testnet-Home**  
     - uses for your BitShares Private Testnet Project
	
	
* Example files location for this section: /dev.bitshares.works/docs/development/testnets/

  - `prv_Get-Dev-Key-used-in-my-genesis.txt (get_dev_key) <https://github.com/bitshares/dev.bitshares.works/blob/master/docs/development/testnets/prv_Get-Dev-Key-used-in-my-genesis.txt>`_
  - `prv_my-genesis-example-Private-testnet.json (my-genesis.json) <https://github.com/bitshares/dev.bitshares.works/blob/master/docs/development/testnets/prv_my-genesis-example-Private-testnet.json>`_ 
  - `prv_config-ini-example.txt  (config.ini) <https://github.com/bitshares/dev.bitshares.works/blob/master/docs/development/testnets/prv_config-ini-example.txt>`_
  
 

	
-----------

.. warning:: For the private testnet, you **MUST** create and set up own genesis file and also, set parameters' values into a database configuration file (config.ini).  You should not connect to the mainnet nor the public testnet.

|

Downloading BitShares Core (testnet branch) and Build 
========================================================

This section includes instructions for the following:

* Downloading BitShares Core 
* Building two application program files (witness_node and cli_wallet)

|

1.Downloading BitShares Core – Testnet branch – source files 
-------------------------------------------------------------------

  A. Open a command window and move to ``$HOME`` directory

  B. Run the following commands to connect Github BitShares Care repository, create a directory ``bitshares-testnet`` for the source code. Move the directory to the ``bitshares-testnet`` folder, and checkout a ``testnet`` branch. 

  ::
	
	git clone https://github.com/bitshares/bitshares-core.git bitshares-testnet
	cd bitshares-testnet/
	git checkout testnet
	git submodule update --init -–recursive

	
	
  C. Review the source files/folders in the ``$HOME/bitshares-testnet`` directory.

|

	
2. Building two application program files (witness_node and cli_wallet)
-------------------------------------------------------------------

  A. Make sure ``$HOME/bitshares-testnet`` directory is your current directory
  B. Perform the initial compilation to build two program files
  C. Run the following commands

  ::

	cmake .
	make

	 
	 
  D. Make sure if the application files were created successfully

- File locations 

.. list-table::
   :widths: 20 80
   :header-rows: 0
   
   * - cli_wallet program 
     - $HOME/bitshares-testnet/programs/cli_wallet/cli_wallet
   * - witness_node program 
     - $HOME/bitshares-testnet/programs/witness_node/witness_node
	
--------------

|


Preparing a genesis file for the Private Testnet 
==========================================================

This section provides instructions for preparing the genesis file for the Private Testnet.  The genesis file is the initial state of the network. For the private testnet preparation, you must set up (generate) each key value in the genesis file.

* Creating my-genesis.json file 
* Customizing my-genesis.json
* Embedding my-genesis.json after completed customization of a genesis file (Optional) 


**Customizing a genesis file allows to control the followings:**
	- The accounts that exist at genesis, their names and public keys
	- Assets and their initial distribution (including core asset)
	- The initial values of chain parameters
	- The account / signing keys of the init witnesses (or in fact any account at all).

	
**Note:**
	- For a Private Testnet setup, a genesis-dev.json file can be used as a template. However, you must replace each key value for the private testnet. Each key starts with "TEST" prefix. 
	- The core token symbol in testnet is "TEST" (hardcoded in binaries, cannot be changed in genesis file). 

|
	
	
1.Creating my-genesis.json file
-------------------------------------------------------------------

  A. Create genesis directory

   - Make sure your current directory is ``$HOME/bitshares-testnet``
   - Create a genesis directory  

  ::

	# For example: 
	mkdir genesis


  B. Create ``my-genesis.json`` file as a blank file    

   - Move to the genesis directory  (i.e., $HOME/bitshares-testnet/genesis/)
   - Create a file and name “my-genesis.json”  


  C. Copy a template genesis-dev.json file.  If you have created my-genesis.json file already, skip this step.

   - Use the genesis-dev.json file as a guide to prepare genesis file parameter values. 
   - Each Private Testnet should prepare own ``my-genesis.json`` file to run.
   - The genesis-dev.json locates ``$HOME/bitshares-testnet/libraries/egenesis/genesis-dev.json``.  Copy this file to the genesis directory (It was created in step A).

  ::
  
	# For example:

	cp -p $HOME/bitshares-testnet/libraries/egenesis/genesis-dev.json  $HOME/bitshares-testnet/genesis/my-genesis.json
  
  
.. attention:: Do NOT use (or copy) a genesis.json file from the Mainnet repository.

|

2.Customizing my-genesis.json
-------------------------------------------------------------------

  A. View my-genesis.jon and familiar with parameters. 

   - Some parameters have default values (recommended).  
   - Example and Tips – :ref:`Private Testnet Genesis File  <private-testnet-genesis-example>` 

  B. Generate private and public key pairs by ``get_dev_key`` function. The ``get_dev_key`` function can be used by using ``../programs/genesis_util/``.  

	**Note:**
	This ``get_dev_key`` function is simply combine the ``prefix`` with all given ``suffixes``, compute a hash over each combination, and use that hash as a private key, from which the matching public key and address are generated.

	
   - Move to the ``$HOME/bitshares-testnet/programs`` directory
   - Run the command

  ::
	
	# For example:
	./genesis_util/get_dev_key wxyz- testkey1 testkey2 testkey3

	
	
See, :ref:`how-to-get-key-pairs` for more information 



|

	
3.Embedding my-genesis.json file (Optional)
-------------------------------------------------------------------

**IMPORTANT:** Before you embed my-genesis.json, ensure the genesis file setting has been completed, otherwise skip this section.


.. tip:: Embedding the genesis copies the entire content of genesis.json into the witness_node binary, and additionally copies the chain ID into the cli_wallet binary. Embedded genesis allows the following simplifications to the subsequent instructions:

 - You do **not** need to specify the ``my-genesis.json`` file on the witness node command line, or in the witness node configuration file.
 - You do **not** need to specify the **chain ID** on the ``cli_wallet`` command line when starting a new wallet.

-----
   
  A. Make sure a current directory is ``$HOME/bitshares-testnet/`` 
  B. Run the following command. Use the **full path** to the my-genesis.json

  ::
  
    # For example:
	cmake -DGRAPHENE_EGENESIS_JSON="$HOME/bitshares-testnet/genesis/my-genesis.json"

	
  C. Rebuild the programs 

  ::
  
	make


**Note:**  Embedded genesis is a feature designed to make life easier for consumers of pre-   compiled Binaries, in exchange for slight, optional complication of the process for producing binaries.

------

**INFORMATION:**
If getting trouble to embed a genesis file. Clean the build and Make cache variables for GRAPHENE_EGENESIS_JSON to take effect. 

  ::
  
	# For example:
	make clean
	find . -name "CMakeCache.txt" | xargs rm -f
	find . -name "CMakeFiles" | xargs rm -Rf
	cmake -DGRAPHENE_EGENESIS_JSON="$(pwd)/genesis/my-genesis.json" .


**Warning:**  Deleting caches will reset all ``cmake`` variables, so if you have used instructions like build-ubuntu which tells you to set other ``cmake`` variables, you will have to add those variables to the ``cmake`` line above.

------------------

|

Creating Another Directory for the BitShares Private Testnet Project
==========================================================================

This section includes instructions for the following:

* Creating another directory for BitShares Private Testnet Project
* Copying two application files into the BitShares Private Testnet Project directory
* Creating a genesis directory for this Private Testnet 
* Copying a genesis file that completed in the previous section

|


1.Creating another directory for BitShares Private Testnet Project
-------------------------------------------------------------------

  A. Make sure, your current directory is ``$HOME`` 
  B. Create a directory name “Testnet-Home”

  ::
	  
	# For example:
	mkdir Testnet-Home


|

2.Copying the two program files (witness_node and cli_wallet) in the Testnet-Home directory
------------------------------------------------------------------------------------------------

  A. Use the below as examples

  ::
  
	cp -p $HOME/bitshares-testnet/programs/cli_wallet/cli_wallet $HOME/Testnet-Home/cli_wallet

	cp -p $HOME/bitshares-testnet/programs/witness_node/witness_node $HOME/Testnet-Home/witness_node

	
  B. Ensure the two program files are in ``$HOME/Testnet-Home/`` directory 


|

3.Creating a genesis directory for the Private testnet 
-------------------------------------------------------------------

If your genesis file has been embedded, you do not need to move the genesis file.  Skip to the next section (Setting up a Blockchain Data Directory).

  A. Make sure, your current directory is ``$HOME/Testnet-Home/``
  B. Create a directory and name “genesis”

  ::
  
	# For example:
	mkdir genesis


|

4.Copying a prepared genesis file 
-------------------------------------------------------------------

  A. Use the below as an example

  ::
  
	cp -p $HOME/bitshares-testnet/genesis/my-genesis.json $HOME/Testnet-Home/genesis/my-genesis.json

  B. Ensure the ``my-genesis.json``  is in ``$HOME/Testnet-Home/genesis/`` directory 

--------------------

|


Setting up a Blockchain Data Directory 
===================================================


This section provides instructions for preparing a blockchain data directory and obtaining a configuration file. The configuration file will be created (if it’s not existed) during ``witness_node`` start up and found in the data directory to setup the project environment parameters.

* Creating a Data directory for the Private Testnet Project
* Obtaining a configuration file (config.ini) 
* Viewing the data directory
* Viewing the configuration file parameters
* Setting up the configuration parameters


.. TIP:: 
	- ``witness_node`` startup will create a ``witness_node_data_dir`` as a default data directory. A configuration file will be created in the data directory. To obtain the config.ini file, start the witness_node and stop (CTRL + C). And setup the configuration file values. 
	- The data directory can be created to a different location and name by using ``--data-dir`` option in a ``witness_node`` startup command line.  
	- If do not want to use the default data directory ``witness_node_data_dir``, use the ``--data-dir`` option to point the data directory path, every time ``witness_node`` start running again.


|


1.Creating a Data directory for the Private Testnet Project
-------------------------------------------------------------------

  A. Make sure, a current directory is ``$HOME/Testnet-Home/``
  B. Create a data directory for the Private Testnet blockchain 

  ::
  
	# For example:
	mkdir data-bts


|

2.Obtaining a configuration file (config.ini) 
-------------------------------------------------------------------

  A. Make sure, your current directory is ``$HOME/Testnet-Home/``
  B. Run one of the following command lines:  

  ::
	  
	./witness_node --data-dir data-bts/my-blocktestnet --genesis-json genesis/my-genesis.json --seed-nodes "[]"

	 // OR

	./witness_node --data-dir=data-bts/my-blocktestnet --genesis-json=genesis/my-genesis.json --seed-nodes="[]"


  - ``--data-dir`` : defines the data directory and a blockchain folder
  - ``--genesis-json`` : defines the genesis file directory and my-genesis.json file for this Private Testnet 
  - ``--seed-nodes`` : with “[]” (*double quotes are required).  This creates a list of empty seed nodes to avoid connecting to default hardcoded seeds. 

  **Known issue:** Missing = (equal sign) between input parameter and value. --> This is due to a bug of a boost 1.60. If you compile with boost 1.58, the = (equal sign) can be omitted.

  .. Note:: If my-genesis.json file has been embedded, you do not need to specify the path ro the genesis file. Omit, ``--genesis-json genesis/my-genesis.json``
  
  
  C. Stop the ``witness_node``.  Use ``CTRL + C``. 


  D. Review the screen output. You will find a Chain ID like the below (**your Chain ID should be different**). 

  ::
  
	# For example: 
	3501235ms th_a main.cpp:165 main] Started witness node on a chain with 0 blocks.
	3501235ms th_a main.cpp:166 main] Chain ID is cf307110d029cb882d126bf0488dc4864772f68d9888d86b458d16e6c36aa74b
		

|

3.Viewing the data directory
-------------------------------------------------------------------

  A. Move to the data directory 
 
  ::
  
	 # For example:
	 cd data-bts/my-blocktestnet

  B. Review the output and ensure the blockchain data folder has been created successfully

  ::
  
	# For example: 
	my-blocktestnet/blockchain/
	my-blocktestnet/logs/
	my-blocktestnet/p2p/
	my-blocktestnet/config.ini
	my-blocktestnet/loging.ini


|

4.Viewing the configuration file parameters
-------------------------------------------------------------------

  A. Open the configuration file (config.ini) with your editor. 

  - Several parameters already have default values (recommended).  
  - For more detailed information on the Private Testnet configuration file. See, :ref:`Configuration for Private Testnets - config.ini <bts-config-ini-eg-private-testnet>`


|

5.Setting up the configuration file parameters
-------------------------------------------------------------------

  A. Uncomment parameters if necessary and set each value  

* Example: 

::

	# Endpoint for P2P node to listen on
	p2p-endpoint = 127.0.0.1:11010
	
	# Endpoint for websocket RPC to listen on
	rpc-endpoint = 127.0.0.1:11011
	                
	###--> For Private Testnet, add a seed node of your own
	# P2P nodes to connect to on startup (may specify multiple times)
	# seed_node =  
	
	###--> For Private Testnet, this value set needs to overwrite default checkpoint.
	checkpoint = []
	# Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints.
	## checkpoint = ["22668518", "0159e4e600cb149e22ef960442ca331159914617"]
	
	# File to read Genesis State from
	genesis-json = genesis/my-genesis.json
		
	# ==============================================================================
	# witness plugin options
	# ==============================================================================

	# Enable block production, even if the chain is stale.
	enable-stale-production = false

	# Percent of witnesses (0-100) that must be participating in order to produce blocks
	# required-participation = 33 
	# If start a private testnet with the default number 33, the node won't produce blocks	
	####--> For Private testnet, set 0 
	required-participation = 0 
	
	###--> For Private Testnet, set own key pairs
	# Tuple of [PublicKey, WIF private key] (may specify multiple times)
	private-key = ["-- generated key --","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]

	# ID of witness controlled by this node (e.g. "1.6.5", quotes are required, may specify multiple times)
	# witness-id =
	witness-id = "1.6.1"
	witness-id = "1.6.2"
	witness-id = "1.6.3"
	witness-id = "1.6.4"
	witness-id = "1.6.5"
	witness-id = "1.6.6"
	witness-id = "1.6.7"

	

	
	
.. Tip::
 - [private-key] options must match the witness ``block_signing_key`` in the genesis file.
 - [witness-id] numbers refer to the initial witnesses from the genesis file, starting with "1.6.1" for the first witness.
 - The witness-id is not given explicitly in the genesis file. The IDs are assigned sequentially starting from 1, i. e. the first listed witness will have "1.6.1", the second "1.6.2" and so on.
 - The tuples for "private-key" must contain the public block signing key and the corresponding private key of a witness. The node will start generating blocks only if it has one or more "witness-id" options *and* the corresponding private-keys of these witnesses.  
 
 
 
This authorizes the ``witness_node`` to produce blocks on behalf of the listed **witness-id's**, and specifies the private key needed to sign those blocks. Normally each witness would be on a different node, but for the purposes of this testnet, we will start out with all witnesses signing blocks on a single node.

.. note:: It's important to activate a 2/3 majority of the witnesses defined in the genesis file.
 
-----------------------

|


Starting a witness_node 
=========================================

This section provides instructions for starting a ``witness_node`` to produce blocks

* Starting ``witness_node`` with parameter options



|


1.Starting witness_node with parameter options
-------------------------------------------------------------------
  
  A. Make sure, a current directory is ``$HOME/Testnet-Home/``

  B. Start ``witness_node`` with parameter options

  ::
  
	# For example:

	./witness_node --data-dir=data-bts/my-blocktestnet --enable-stale-production --seed-nodes "[]"


* **Note**

  - If you have set a ``my-genesis.json`` file path in a configuration file (config.ini) or embedded the genesis file, do not need to use the ``--genesis-json`` option in this witness_node start up. 
  - The ``--enable-stale-production`` flag tells the ``witness_node`` to produce on a chain with zero blocks or very old blocks. We specify the ``--enable-stale-production`` parameter on the command line as we will not normally need it (although it can also be specified in the configuration file). 
  - The empty ``--seed-nodes`` is added to avoid connecting to the default seed nodes hardcoded for production.  (i.e., # seed-node =   )
  -  Subsequent runs which connect to an existing witness node over the p2p network, or which get blockchain state from an existing data directory, do not need to have the ``--enable-stale-production`` flag.

  



  	
	
-------------------------------

|

Starting cli_wallet to create Cli Wallet
=============================================================

This section includes instructions for the following:

* Obtaining the Chain ID
* Starting a ``cli_wallet``
* Setting a password and Unlock the cli wallet  


.. Important:: Each wallet ``wallet.json`` is specifically associated with a single chain, specified by its chain ID. This is to protect the user from (e.g., unintentionally) using a testnet wallet on the real chain. The Chain ID passed to the cli_wallet needs to match the Chain ID generated and used by the witness node.

**Chain ID::**
The chain ID is a hash of the genesis state. All transaction signatures are only valid for a single chain ID. So, editing the genesis file will change your chain ID, and make you unable to sync with all existing chains (unless one of them has exactly the same genesis file you do).

**wallet.json::**
Each wallet has a ``wallet.json`` file that is associated with a specific chain-id. (i.e., When connecting to a new or different test network you must also use a new or different wallet.json.)


|

1.Obtaining the Chain ID
-------------------------------------------------------------------

When we started the ``witness_node``, the Chain ID appeared on the screen. If you have saved the Chain ID, skip this step. 

If you are not sure about the chain ID, it can be obtained by using the API to query a running witness node with the ``get_chain_properties`` API call.


  A. Obtain the Chain ID by using API call. This ``curl`` command will return a short JSON object including the ``chain_id``

  ::
  
	# For example:
	curl --data '{"jsonrpc": "2.0", "method": "get_chain_properties", "params": [], "id": 1}' http://127.0.0.1:11011/rpc && echo


**Note:**
For testing purposes, the ``--dbg-init-key`` option will allow you to quickly create a new chain against any genesis file, by replacing the witnesses’ block production keys.


|


2.Starting a cli_wallet
-------------------------------------------------------------------

This will connect a new wallet to your Private Testnet witness node. You must specify a chain ID (if you did not embed a genesis file) and server. Keep your witness node running. Open another Command Prompt window and move to your Private Testnet Project directory. 


  A. Make sure, a current directory is ``$HOME/Testnet-Home/``
  B. Start a cli_wallet

  ::
  
	# For example: 
	./cli_wallet --wallet-file my-wallet.json
			   --chain-id cf30711----USE-OWN-CHAIN-ID---68d9888d86b458d16e6c36aa74b
			   --server-rpc-endpoint ws://127.0.0.1:11011 -u '' -p ''

	# For example: (if a genesis file has been embedded)
	./cli_wallet --wallet-file my-wallet.json 
			   --server-rpc-endpoint ws://127.0.0.1:11011 -u '' -p ''
			   
			   
.. Note:: 
  - ``--wallet-file`` : define the wallet file path (if it does not exist, it will be created when close the cli_wallet).
  - ``chain-id`` : Obtained Chain ID from the ``witness_node`` startup.
  - ``server-rpc-endpoint`` : The port number is how you defined (opened) ``--rpc-endpoint`` for the witness_node.


If it connected (executed) successfully, it will prompt ``new >>>``  to set a password.


|

3.Setting a password and Unlock the cli wallet 
-------------------------------------------------------------------

  A. First you need to create a new password to the cli wallet. 

  - **For more detailed instructions, see the tutorial on** :ref:`How to Set a password and Unlock a Cli Wallet <cli-wallet-setpwd-unlock>`


-------------------------------

|

Gaining Access to the Genesis Stake
=============================================================

This section provides instructions for accessing accounts and asset in a genesis file

* Importing key and account name into cli wallet
* Importing a balance into cli wallet
* Viewing the imported account information
* Listing the account balance


|

1.Importing key and account name into cli wallet
-------------------------------------------------------------------

To process this step, we prepared initial accounts and initial balances information in my-genesis.json.  We use “TEST” as initial_balances  asset_symbol and “init30-test” account user who has the balance. 


  A. Make sure your ``witness_node`` is running.
  B. Connect to your cli_wallet and ``unlock`` the wallet 
  C. Import “private key” and “name“. (Importing ``init30-test`` ‘s private key of the active key).

    ::
  
	# For example: 
	import_key init30-test "5JG5thpLiuTG1ANiV9j4EyDHCXjvM67NRLtYSRGhusL5wg9CahY" # active_key
	
	import_key init30-test "5JU3yZnDy5Gf9gS4iQwSS1zDLzP3ECmRfWv6kx76WxnufTQRAqr"  # owner_key

	
.. Note:: The active key is equal to the memo key, which is required for transfers with a memo.

|


2.Importing a balance into cli wallet
-------------------------------------------------------------------

We prepared the genesis file for this example. So, we know that the ``init30-test`` account has “TEST” asset balance to import. 

    ::
  
	# For example: 
	import_balance init30-test ["5JU3yZnDy5Gf9gS4iQwSS1zDLzP3ECmRfWv6kx76WxnufTQRAqr"] true


|


3.Viewing the imported account information 
-------------------------------------------------------------------

We imported one account information into the cli wallet. Review the account information. 

    ::
  
	# For example: 
	get_account init30-test


|


4. Listing the account balance
-------------------------------------------------------------------

View the user account balance.


    ::
  
	# For example: 
	list_account_balances init30-test

-------------------------------

|

Creating Another Account
=============================================================
	
This section provides instructions for creating new account. We will create new account ``alpha-test`` and transfer funds “TEST” between ``init30-test`` and ``alpha-test``.

* Checking a lifetime membership status
* Registering new account
* Transferring funds between accounts 
* Finding the private key
* Upgrading an account


.. Note::
   Creating a new account is always done by using an existing account (e.g., init30-test).  When creating new account, someone (i.e., the register) has to fund the registration fee. Also, there is the requirement for the register account to have a LifeTime Membership (LTM) status


|
	
1.Checking a lifetime membership status
-------------------------------------------------------------------

  A. View the register account information to see if it has the lifetime membership status.

    ::
  
	# For example: 
	get_account init30-test

  B. Check ``membership_expiration_date``; you should see a future date (e.g., "membership_expiration_date": "2106-02-07T06:28:15"). If you get ``1970-01-01T00:00:00`` something is wrong, or the register does not have a lifetime membership yet. 
  C. Upgrade an account status to a lifetime member. 

    ::
  
	# For example: 
	upgrade_account init30-test true



**Note:** The account to be upgraded must have funds to pay a lifetime membership fee as specified in the genesis file. 


|


2.Registering new account
-------------------------------------------------------------------

We register new account ``alpha-test`` by using ``init30-test`` account as a registrar. First, we generate public and private key pairs for new account. 


  A. Generate new key pairs. Use ``suggest_brain_key`` function

    ::
  
	# For example: 
	unlocked >>> suggest_brain_key
	{
	  "brain_priv_key": "MOCMAIN LYRIST AVIDLY GRUMBLE BAROI BRACHET UNDULY PLUMERY BORREL ROOIBOK HADJ DARST KOALA MISTER YTTRIC MORELLA",
	  "wif_priv_key": "5Jr5C8fxniR7n2B1ipfZPpw39FReeSBAQVTi4cAQANxuT96eWiT",
	  "pub_key": "TEST721w2dfphe1uChWPdpotYqwxzPavzzoTf3dBdq8pahrd1rK1su"
	}

 
  B. Register new account. The register_account command allows you to register an account by using only a public key. 

    ::
  
	# For example: 
	register_account alpha-test TEST721w2dfphe1uChWPdpotYqwxzPavzzoTf3dBdq8pahrd1rK1su TEST721w2dfphe1uChWPdpotYqwxzPavzzoTf3dBdq8pahrd1rK1su init30-test init30-test 0 true

	 
  C. View new account information. Examine the output, check each key (owner, active, and memo_key).

    ::
  
	# For example: 
	get_account alpha-test


|
	

3. Transferring funds between accounts
-------------------------------------------------------------------

  A. Transfer funds (30,500 TEST) from ``init30-test`` to ``alpha-test``, without memo.  And check the balance.

    ::
  
	# For example: 
	transfer init30-test alpha-test 30500 TEST "" true 

	list_account_balances alpha-test


 
  B. Transfer funds with “memo”.  We need to import a private key of the memo key. The memo key is the same as the active key (in this example). 

    ::
  
	# For example: 
	import_key init30-test "5JG5thpLiuTG1ANiV9j4EyDHCXjvM67NRLtYSRGhusL5wg9CahY" 

	
  C. Transfer funds (4,000 TEST) from ``init30-test`` to ``alpha-test``, with memo.  And check the balance.

    ::
  
	# For example: 
	transfer init30-test alpha-test 4000 TEST "Your memo here" true 
	list_account_balances alpha-test


**Note:** "active_key == memo_key" applies to genesis accounts, it's not a general rule.

|

4.Finding a Private Key
-------------------------------------------------------------------

There is a function to find a private key from a public key. 

 
  A. Use ``get_private_key`` function to find a private key. Run the command.

    ::
  
	# For example: 
	get_private_key  TEST6G5BQQfPLRGzBjFUZ87BfSdYL8DgkWk3BLfHu6crznc94BfrJS 

	
.. tip:: This works only for private keys stored in your wallet, and ``get_private_key`` command is useful if you want to import the key into a different wallet (e.g., web wallet).


|

5.Upgrading an account
-------------------------------------------------------------------

New account ``alpha-test`` has received enough funds to upgrade a membership from Basic to LifeTime. 


  A. Try ``upgrade_account`` to give a LifeTime Membership and review the outputs.

    ::
  
	# For example: 
	upgrade_account alpha-test true 

	get_account alpha-test 
	list_account



-------------------------------

|

Creating Committee Members (optional)
=============================================================
	
This section provides instructions for creating committee members' account.  

* Creating a Committee Account 
* Transfer funds
* Upgrade a Membership
* Registering as committee members
* Voting for the committee accounts


You would need the committee if you want to change some chain settings, like fees.

- Read more about the `Committees roles in how.bitshares.works. <http://how.bitshares.works/en/latest/bts_holders/community_members.html#committees>`_

|

1.Creating a Committee Account
-------------------------------------------------------------------

  A. Use ``create_account_with_brain_key`` to create committee accounts and examine the account.  

    ::
  
	# For example: 
	create_account_with_brain_key com0-test com0-test init2-test init2-test true
	create_account_with_brain_key com1-test com1-test init2-test init2-test true
	create_account_with_brain_key com2-test com2-test init2-test init2-test true

	get_account com0-test


|


2.Transfer Funds
-------------------------------------------------------------------

  A. Transfer enough funds to set up a lifetime membership.  

    ::
  
	# For example: 
	transfer init2-test com0-test 21000 TEST "" true
	transfer init2-test com1-test 21500 TEST "" true
	transfer init2-test com2-test 30000 TEST "" true


|

3.Upgrade a Membership
-------------------------------------------------------------------

  A. Transfer funds to set a lifetime membership.  

    ::
  
	# For example: 
	upgrade_account com0-test true
	upgrade_account com1-test true
	upgrade_account com2-test true

 
|

4.Registering as committee member
-------------------------------------------------------------------

  A. Create Committee Members

    ::
  
	# For example: 
	create_committee_member  com0-test "http://www.com0-test" true
	create_committee_member  com1-test "http://www.com1-test" true
	create_committee_member  com2-test "http://www.com2-test" true
	
	
	
|

5.Voting for the committee accounts
-------------------------------------------------------------------

  A. Vote for our own committee members. Elect them and wait for the maintenance interval, the votes become active.

    ::
  
	# For example: 
	vote_for_committee_member init2-test com0-test true true
	vote_for_committee_member init2-test com1-test true true
	vote_for_committee_member init2-test com2-test true true
	

|

-----------------

Setting up the Second Node
=============================================================

If you want to set up a second node (with the same genesis file) and connect it to the first node by using the ``p2p-endpoint`` of the first node as the ``seed-node`` for the second. The below are example settings.


**Node-001: config.ini**

::

	p2p-endpoint = 127.0.0.1:11010
	# seed-node =                // add a seed node of your own
	
	rpc-endpoint = 127.0.0.1:11011
	
	
**Node-002: config.ini**

  - Set the Node-001's ``p2p-endpoint`` as the Node-002's ``seed-node``. 
  
::

	p2p-endpoint = 127.0.0.1:11015
	seed-node = 127.0.0.1:11010	
	
	rpc-endpoint = 127.0.0.1:11020
		

	
.. important:: 

  - The config files should **not** contain the same witness ids and private keys. Otherwise, both nodes would produce blocks simultaneously, which means they would end up on different chain forks.
  - Each node should use only a subset of the witnesses, so block production alternates between them.
  - The log output of each node should show blocks received from the other node. (i.e., got_block....)

  
|


