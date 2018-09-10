

.. _run-cli-wallet-steps:

***********************
Launch a Cli Wallet 
***********************

The following will explain about the installation and how to use the console wallet (not GUI).


.. contents:: Table of Contents
   :local:
   
-------



Steps
=======

1.Installation and build 
----------------------------

- 1a) Install and build it from BitShares-Core
  - BitShares offers you to install BitShares-Core into different platforms; Ubuntu (x64), OSx, and Windows.  Please refer :ref:`installation`.

- 1b) Use the :ref:`CLI-Wallet tools for Windows (x64)`
  - This option you do not need to install BitShates Core. To obtain the tool, go to a `BitShares release page <https://github.com/bitshares/bitshares-core/releases>`_ and download the BitShares-Core-\*-x64-cli-tools.zip. 


To reduce compilation time, you can tell the compile infrastructure to only compile the witness node by running.::

	$ make cli-wallet

instead of::
	
	$ make


  
2. Launching a cli_wallet
----------------------------

The cli_wallet creates a local wallet.json file that contains the encrypted private keys required to access the funds in your account. It **requires** a running witness node (not necessarily locally) and connects to it on launch. 

::

	programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090

Depending on the actual chain that you want to connect to your may need to specify –chain-id.

Examples
^^^^^^^^^

The below section shows two example patterns how to launch the cli_wallet. The first example, we use the public API server node to connect the cli_wallet and also open WebSocket RPC or RPC-HTTP ports. The cli_wallet opens an RPC port for Wallet operations (i.e., spend, buy, sell...). The second example, we use an IP address (localhost) and also open the port for HTTP-RPC.

- \<Examples\> 
  
  - 1.Connecting a Cli-Wallet by using the public API server node
  - 2.Connecting a Cli-Wallet by an IP address (localhost)


**\<Example 1\> Connecting a Cli-Wallet by using the public API server node**

We use the public API node of OpenLedger ``wss://bitshares.openledger.info/ws`` and connect via secured WebSocket connection:

::

    ./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws 
                                     -H 127.0.0.1:8092 
                                     -r 127.0.0.1:8093

This will open the cli-wallet and two RPC ports.  In order to allow RPC calls for wallet operations (spend, buy, sell, …), you need to specify the RPC port(s) and set a parameter to choose between WebSocket RPC ``-r``  or RPC-HTTP ``-H`` requests. 

.. Note::  The cli-wallet can open a RPC port so that you can interface your application with it. You have the choices of **Websocket RPC** via the ``-r`` parameter, and **HTTP RPC** via the ``-H`` parameter


**\<Example 2\> Connecting a Cli-Wallet by an IP address (localhost)**

::

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090
                                     -H 127.0.0.1:8091
                                    
This will open the port 8091 for HTTP-RPC requests and has the capabilities to handle accounts (by Wallet API Calls) while the witness_node can only answer queries to the blockchain.


After opening the cli-wallet, if you have not had a local wallet yet, you will receive ``>>> new`` prompt to provide a pass-phrase for the local wallet. Once a wallet has been created (default wallet file is ``wallet.json``), then it will prompt with ``locked >>>``.


3.Unlock the Cli_Wallet
----------------------------

If you open the cli-wallet successfully, you will receive ``new >>>`` prompt (if no local wallet found). Set a password and unlock the cli-wallet.

::

    new >>>

- ``set_password`` and ``unlock``

(e.g.) we used `supersecretpassphrase` as a password. This password is used to encrypt the private keys in the wallet.

.. Note::  The passphrase is given in clear text and is echoed by the wallet!


::

    new >>> set_password supersecretpassphrase
    set_password supersecretpassphrase
    null
	
    locked >>> unlock "supersecretpassphrase"   
    unlocked >>>

.. Note:: After this point, you can issue any command available to the cli-wallet (Wallet APIs) or construct your own transaction manually.


- ``gethelp``

You can get more detailed information by issuing ``gethelp``. Detailed explanations for most calls are available. For example, 

::

      unlocked >> gethelp "list_account_balances"

	  
.. Note:: Many calls have a obligatory ``broadcast``  -flag as last argument. If this flag is ``False``, the wallet will construct and sign, but **not** broadcast the transaction. This can be very useful for a cold storage setup or to verify transactions.
	  

4. Gain Access to Blockchain
----------------------------------

In Graphene, balances are contained in accounts. To claim an account that exists in the Graphene genesis, **imports the private key for an existing account**. The private key must match either an owner key or an active key for the named account.

- ``import_key``

::

    >>> import_key <name> "<wifkey>"

Funds are stored in genesis balance objects. These funds can be claimed, with no fee, by using the ``import_balance`` command. This call will construct transaction(s) that will claim all balances controlled by wif_keys and deposit them into the given account.

- ``import_balance``

::

    >>> import_balance <name> ["*"] true


5. Create Accounts
-------------------------------

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

    >>> register_account alpha GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF faucet faucet 0 true

	
.. _transfering-funds-cli-wallet:	
	
6. Transfer Funds by using the Cli-wallet
----------------------------------------------

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



7. Obtain the private key
----------------------------

The ``get_private_key`` command allows us to obtain the **private key** corresponding to the block signing key.::

    >>> get_private_key(<pubkey>) 
   

    >>> get_private_key GPH6viEhYCQr8xKP3Vj8wfHh6WfZeJK7H9uhLPDYWLGCRSj5kHQZM


|

|

