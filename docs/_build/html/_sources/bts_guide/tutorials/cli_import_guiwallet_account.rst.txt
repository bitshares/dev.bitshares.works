
.. _howto-import-gui-wallet-account-cli:

*******************************************************
How to import a GUI-wallet account into CLI-wallet
*******************************************************

.. contents:: Table of Contents
   :local:

--------------------

CLI and GUI wallet are two separated applications. They use separated ways to represent backups. You can manually import keys from the GUI into the CLI to have a CLI-wallet.

Your wallet private keys have extremely important roles. By importing your private keys to a new CLI-wallet, you can control your account funds from the CLI-wallet. 

1. Find your private keys in your GUI-wallet
-------------------------------------------------

  1. Login to your GUI-wallet
  2. Go to [Settings] – [Permissions]. There are Active, Owner, and Memo tabs. 
  3. In the each tab, click your public key (or the key image). It will open a private key viewer.
  4. On the form, click [Show], (it might ask you to login) save your each private key and public key information to use later.
  
  
.. Important::  Keep the private keys information to a safe place. 

2. Connect to a CLI-wallet pointing it to a live node
-----------------------------------------------------------

**Example:**::

    ./programs/cli_wallet/cli_wallet --server-rpc-endpoint ws://localhost:8090
    Or 
    ./programs/cli_wallet//cli-wallet -s wss://bitshares.openledger.info/ws

You should get a prompt::

    new>>>
    
3. Set a password and unlock    
--------------------------------------

Set a password for your CLI-wallet and unlock.

.. Note::  This password does not need to be the same with your GUI-wallet password. You will create a *new wallet* and it will be secured by the new password.

::

    new >>> set_password mypass123
    set_password mypass123
    null
    
    locked >>> unlock mypass123
    unlock mypass123
    null
    unlocked >>>

	
4. import key(s)
-----------------------------

Import your each private key you saved from your GUI-wallet into your new CLI-wallet.::

    import_key your-account-name ThisIsThePrivateKeyYouSaved

And you are done. No need to claim balance. Your account balances are in there. 

.. Note:: If you use the wallet to transfer funds, you just need to import the active private key. However, if you have different Active and Memo keys and use a Memo when you transfer, import the memo private key also. 

5. Check your account
-------------------------------

Use ``list_my_account``s to see your imported account.

And to check balance::

    unlocked >>> list_account_balances your-account-name
    list_account_balances your-account-name
    31016.69330 BTS
    0 CNY
    0 USD

    unlocked >>>


Often Used Commands 
---------------------------

:``set_password``:  Sets a new password on the wallet.| The wallet must be either `new’ or ‘unlocked’ to execute this command.   
:``unlock``:   Unlocks the wallet.  
:``gethelp``:   (e.g., gethelp "list_accounts")  |  Returns detailed help on a single API command.
:``info``:   to view the current synchronization 
:``about``: Returns info such as client version, git version of graphene/fc, version of boost, openssl. 
:``import_key``: `import_key <name> "<wifkey>"` |  Imports the private key for an existing account. | The private key must match either an owner key or an active key for the named account. 
:``list_my_accounts``:  to view the current synchronization | Lists all accounts controlled by this wallet. This returns a list of the full account objects for all accounts whose private keys we possess.
:``list_account_balances``:   a list of the given account’s balances | List the balances of an account. Each account can have multiple balances, one for each type of asset owned by that account. The returned list will only contain assets for which the account has a nonzero balance 
:``get_account``:   Returns information about the given account.
:``import_balance``:   import_balance <name> ["*"] true | This call will construct transaction(s) that will claim all balances controled by wif_keys and deposit them into the given account
:``suggest_brain_key``:  Suggests a safe brain key to use for creating your account. create_account_with_brain_key() requires you to specify a ‘brain key’,  
  | create_account_with_brain_key| the signed transaction registering the account  | Creates a new account and registers it on the blockchain. 
:``dump_private_keys``:  Dumps all private keys owned by the wallet. | The keys are printed in WIF format. You can import these keys into another wallet using import_key()       
:``upgrade_account``:   upgrade_account faucet true  | Upgrades an account to prime status. This makes the account holder a ‘lifetime member’. 
:``register_account``:    `register_account <name> <owner-public_key> <active-public_key> <registrar_account>  <referrer_account> <referrer_percent> <broadcast>` | Registers a third party’s account on the blockckain. 
:``transfer``:    `transfer <from> <to> <amount> <asset> <memo> <broadcast>` | Transfer an amount from one account to another. 
:``transfer2``:    `transfer2 <from> <to> <amount> <asset> <memo> <broadcast>`  | This method works just like transfer, except it always broadcasts and returns the transaction ID along with the signed transaction. 
:``get_account_history``:    e.g., get_account_history "name" "5" | Returns the most recent operations on the named account.     
:``get_privatre_key``:     get_privatre_key <public key>  | Get the WIF private key corresponding to a public key. The private key must already be in the wallet. 

|

|


