

.. _run-cli-wallet-steps:


Launch a Cli Wallet 
***********************

The following will explain about the installation and how to use the console wallet (not GUI).


.. contents:: Table of Contents
   :local:
   
-------

**Steps**


1.Installation
==================================

- 1a) Build from source
  - Please refer :ref:`Installation Guide <installation-guide>`.

  To reduce compilation time, you can tell the compile infrastructure to only compile the cli wallet by running::

	$ make cli_wallet

  instead of::
	
	$ make

- 1b) Use the prebuilt binaries
  - Go to the `BitShares release page <https://github.com/bitshares/bitshares-core/releases>`_ and download the binaries.


|
  
2. Launching a cli_wallet
==================================

The cli_wallet creates a local wallet.json file that contains the encrypted private keys required to access the funds in your account. It **requires** a running witness node (not necessarily locally) and connects to it on launch. 

::

	programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090

Depending on the actual chain that you want to connect to your may need to specify --chain-id.

Examples
^^^^^^^^^

The below section shows two example patterns how to launch the cli_wallet. The first example, we use a public API server node to connect the cli_wallet and also open WebSocket RPC or RPC-HTTP ports. The cli_wallet opens an RPC port for Wallet operations (i.e., spend, buy, sell...). The second example, we use an IP address (localhost) and also open the port for HTTP-RPC.

- \<Examples\> 
  
  - 1.Connecting a Cli-Wallet by using a public API server node
  - 2.Connecting a Cli-Wallet by an IP address (localhost)


**\<Example 1\> Connecting a Cli-Wallet by using a public API server node**

We use the public API node of bts.mobi ``wss://api.bts.mobi/ws`` and connect via secured WebSocket connection:

::

    ./programs/cli_wallet/cli_wallet -s wss://api.bts.mobi/ws 
                                     -H 127.0.0.1:8092

This will open the cli-wallet and a RPC port.  In order to allow RPC calls for wallet operations (spend, buy, sell, …), you need to specify the RPC port. 

.. Note::  The cli-wallet can open a RPC port so that you can interface your application with it. It accepts **Websocket RPC** and **HTTP RPC**.


**\<Example 2\> Connecting a Cli-Wallet by an IP address (localhost)**

::

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090
                                     -H 127.0.0.1:8091
                                    
This will open the port 8091 for HTTP-RPC requests and has the capabilities to handle accounts (by Wallet API Calls) while the witness_node can only answer queries to the blockchain.


After opening the cli-wallet, if you have not had a local wallet yet, you will receive ``>>> new`` prompt to provide a pass-phrase for the local wallet. Once a wallet has been created (default wallet file is ``wallet.json``), then it will prompt with ``locked >>>``.

|

.. _3-unlock-cli-wallet:

3.Unlock the Cli_Wallet
==================================

If you open the cli-wallet successfully, you will receive ``new >>>`` prompt (if no local wallet found, you will be asked to set a password). The following shows how to use cli_wallet unlock/lock commands. 

::

	Please use the set_password method to initialize a new wallet before continuing
	new >>> 
	
	
* **For more detailed instructions, see the tutorial on** :ref:`How to Set a password and Unlock a Cli Wallet <cli-wallet-setpwd-unlock>`

	  
|

4. Gain Access to Blockchain
==================================

In BitShares, balances are contained in accounts. To claim an account that exists in the genesis, **imports the private key for an existing account**. The private key must match either an owner key or an active key for the named account.

- ``import_key``

::

    >>> import_key <name> "<wifkey>"

Funds are stored in genesis balance objects. These funds can be claimed, with no fee, by using the ``import_balance`` command. This call will construct transaction(s) that will claim all balances controlled by wif_keys and deposit them into the given account.

- ``import_balance``

::

    >>> import_balance <name> ["*"] true

|

5. Create Accounts
==================================

.. Note:: To register an account, the registrar needs to be a lifetime member. You can use ``upgrade_account`` to upgrade the account to be **Lifetime member (LTM)** status.

We upgrade `faucet` account because `faucet` is the registrar in this example below.
 
::
 
    >>> upgrade_account faucet true

- ``register_account``

::

    >>> register_account <name> <owner-public_key> <active-public_key> <registrar_account> <referrer_account> <referrer_percent> <broadcast>

This command allows you to register an account using only a **public key**. 

**\<Example\>**

::

    >>> register_account alpha BTS4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF BTS4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF faucet faucet 0 true

	
.. _transfering-funds-cli-wallet:	
	
|
	
6. Transfer Funds by using the Cli-wallet
==================================

In ``transfer``, if the broadcast flag is ``False``, the wallet will construct and sign, but **not**, broadcast the transaction. 

- ``transfer``::

    unlocked >> transfer <from> <to> <amount> <asset> <memo> <broadcast>
   
**\<Example\>  `faucet` wants to send 100000 `CORE` to `alpha` user.**::

    unlocked >> transfer faucet alpha 100000 CORE "here is the cash" true

The wallet will return the actually signed transaction.

.. Note:: In order to transfer, the wallet must be unlocked. If the broadcast flag is ``False``, the wallet will construct and sign, but **not** broadcast the transaction. This can be very useful for a cold storage setup or to verify transactions.


**\<Example\>  Open a new Wallet for `alpha` user**::

    >>> import_key alpha 5HuCDiMeESd86xrRvTbexLjkVg2BEoKrb7BAA5RLgXizkgV3shs

    >>> upgrade_account alpha true

    >>> create_witness alpha "http://www.alpha" true


|

7. Obtain the private key
==================================

The ``get_private_key`` command allows us to obtain the **private key** corresponding to the public key.::

    >>> get_private_key <pubkey>
   

    >>> get_private_key BTS6viEhYCQr8xKP3Vj8wfHh6WfZeJK7H9uhLPDYWLGCRSj5kHQZM

|



---------------
	
.. tip:: If you want to import your current GUI wallet fund(s), see
  
    * :ref:`How can I import a GUI-wallet account into CLI-wallet? <howto-import-gui-wallet-account-cli>`
	
	
|

|

