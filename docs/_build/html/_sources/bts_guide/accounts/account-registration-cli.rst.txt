
.. _account-create-cli:

Account Registration
======================================

	
.. contents:: Table of Contents
   :local:
   
-------


1. Prerequisites
--------------------

- Connecting to witness_node (Full node or API Node)  
- CLI Wallet *commond window* ready to use.  

2. Create a brain Key and derive a private/public key pair
---------------------------------------------------------------

::

    >>> suggest_brain_key
    {
    "brain_priv_key": "FILINGS THEREOF ENSILE JAW OVERBID RETINAL PILULAR RYPE CHITTY RAFFERY HANDGUN ERANIST UNPILE TWISTER BABYDOM CIBOL",
    "wif_priv_key": "5JVrt2921aikA7QP5ZCtR2sJh4wbEnsHsK6qo67Shnk9ArKMzNT",
    "pub_key": "BTS7D8jpQ2UwaQxqKyGpuhFQ9LBugCNxDhE7UN2jqgjVQgzG7zo9n"
    }

3. Create an Account
-------------------------

Creates a new account and registers it on the blockchain.

::

    >>> create_account_with_brain_key <brain_key> <account_name> <registrar_account> <referrer_account> <broadcast>

**Example**

    >>> create_account_with_brain_key "FILINGS THEREOF ENSILE JAW OVERBID RETINAL PILULAR RYPE CHITTY RAFFERY HANDGUN ERANIST UNPILE TWISTER BABYDOM CIBOL" my-account123 myfunds anonymous true


4. Register an Account
----------------------------

If you want to register the account of someone else, all you need is the public key. In theory, the BitShares blockchain distinguishes three keys for each account, namely the **owner**, **active**, and the **memo** key. However, for the sake of simplicity, we here make use of only one public key.

In order to register an account, we need an other account that has enough funds to pay the fee for the registration transaction. This account will be called `registrar_account`. Another account `referrer_account` can be registered that will get `referrer_percentage` of the referral bonus program. Any registered account can take the role of the referrer. Hence we here say that user `anonymous` has referred us. 

.. Note:: In order to register an account, the registrar (here: myfunds) needs to be a **lifetime member**!

> Note: If you want to register the account of someone else, all you need is the **public key**.

::

    >>> register_account <name> <owner_pubkey> <active_pubkey> <registrar_account> <referrer_account> <referrer_percent> <broadcast>

**Example** 

 We register a new user called mywallet, use the pubkey derived above and let our account myfunds pay the fee::

    >>> register_account mywallet BTS7D8jpQ2UwaQxqKyGpuhFQ9LBugCNxDhE7UN2jqgjVQgzG7zo9n BTS7D8jpQ2UwaQxqKyGpuhFQ9LBugCNxDhE7UN2jqgjVQgzG7zo9n myfunds anonymous 100 true


- ``register_account``

:description: *Registers a third party’s account on the blockckain.*  This function is used to register an account for which you do not own the private keys. When acting as a registrar, an end user will generate their own private keys and send you the public keys. The registrar will use this function to register the account on behalf of the end user.   

  **parameter** 

:[name]:  the name of the account, must be unique on the blockchain. Shorter names are more expensive to register; the rules are still in flux, but in general names of more than 8 characters with at least one digit will be cheap. (e.g., mywallet)   

:[owner_pubkey]:  the owner key for the new account (e.g., "pub_key" value - created by the `suggest_brain_key`)  

:[active_pubkey]: the active key for the new account  (e.g., "pub_key" value - created by the `suggest_brain_key`)

:[register_account]:  the account which will pay the fee to register the user.  (e.g., myfunds)          

:[referrer_account]:  the account who is acting as a referrer, and may receive a portion of the user’s transaction fees. (e.g., anonymous) 

:[referrer_percentage]: the percentage (0 - 100) of the new user’s transaction fees not claimed by the blockchain that will be distributed to the referrer. (e.g., 100) 

:[broadcast]:  true to broadcast the transaction on the network. (e.g., true)   


|