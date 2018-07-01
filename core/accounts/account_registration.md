# Account Registration

In this section, we will show how to use a brain key and a public key while creating/registering an account. 


#### Table of Contents: 

1. [Prerequisites](/core/accounts/account_registration.md#1-prerequisites)
2. [Create a brain Key and derive a private/public key pair](/core/accounts/account_registration.md#2-create-a-brain-key-and-derive-a-privatepublic-key-pair)
3. [Create an Account](/core/accounts/account_registration.md#3-create-an-account)
   - If you want to create a new account and registers it on the blockchain.
4. [Register an Account ](/core/accounts/account_registration.md#4-register-an-account)
   - If you want to register a third party’s account on the blockckain.

*******

## 1. Prerequisites

   - CLI Wallet _commond window_ ready at this point to type in a command.  
   - .... server and connection Witness_node (Full node) 

## 2. Create a brain Key and derive a private/public key pair

Creating an account uses a brain key and a public key. Run the `suggest_brain_key` command in the CLI Wallet to obtain the keys. This command suggests a safe brain key to use for creating an account.

**Example**

    >>> suggest_brain_key
    {
    "brain_priv_key": "FILINGS THEREOF ENSILE JAW OVERBID RETINAL PILULAR RYPE CHITTY RAFFERY HANDGUN ERANIST UNPILE TWISTER BABYDOM CIBOL",
    "wif_priv_key": "5JVrt2921aikA7QP5ZCtR2sJh4wbEnsHsK6qo67Shnk9ArKMzNT",
    "pub_key": "BTS7D8jpQ2UwaQxqKyGpuhFQ9LBugCNxDhE7UN2jqgjVQgzG7zo9n"
    }

The hierarchy for these values goes like this::

     HASH(brain_priv_key) -> wif_priv_key
     HASH(HASH(brain_priv_key)) -> pub_key

### suggest_brain_key

|   return item       |  note            |
| ------------------- |---------- |
| "brain_priv_key"    |  You will be able to recover your required keys to access your account and/or funds. |                           
| "wif_priv_key"      |  private key  (i.g., owner key) |                            
| "pub_key"           |  public Key  |                           

> Note: Even though suggest_brain_key shows only one private key that will be used for the **owner authority** most wallet implementations will derive an additional second private key to be used for the **active authority**!

> Note: The brain key is used as source for all cryptographic keys generated in the wallet. If you have it secured, you will be able to regain access to your accounts and funds (unless the access keys have been changed).

## 3. Create an Account
If you want to create and register a new account on your own because you have the funds in another account and don’t want someone else involved, you can make use of the command `create_account_with_brain_key`::

    >>> create_account_with_brain_key <brain_key> <account_name> <registrar_account> <referrer_account> <broadcast>

**Example**

    >>> create_account_with_brain_key "FILINGS THEREOF ENSILE JAW OVERBID RETINAL PILULAR RYPE CHITTY RAFFERY HANDGUN ERANIST UNPILE TWISTER BABYDOM CIBOL" mywallet myfunds anonymous true

### create_account_with_brain_key

|                     |  `create_account_with_brain_key`  |
| ------------------- |---------- |
|  **description**    |  Creates a new account and registers it on the blockchain.     |
|                     |       |
|  **parameter**          |  **note**     |
| [brain_key]         | "brain_priv_key" value -  the brain key used for generating the account’s private keys  |                           
| [account_name]      | the name of the account, must be unique on the blockchain. Shorter names are more expensive to register; the rules are still in flux, but in general names of more than 8 characters with at least one digit will be cheap.  (e.g., mywallet)  |                            
| [registrar_account] |  the account which will pay the fee to register the user.   (e.g., myfunds)   |                           
| [referrer_account]  | the account who is acting as a referrer, and may receive a portion of the user’s transaction fees.  (e.g., anonymous )    |                            
| [broadcast]         | true to broadcast the transaction on the network. (e.g., true)      |


## 4. Register an Account
If you want to register the account of someone else, all you need is the public key. In theory, the BitShares blockchain distinguishes three keys for each account, namely the **owner**, **active**, and the **memo** key. However, for the sake of simplicity, we here make use of only one public key.

In order to register an account, we need an other account that has enough funds to pay the fee for the registration transaction. This account will be called `registrar_account`. Another account `referrer_account` can be registered that will get `referrer_percentage` of the referral bonus program. Any registered account can take the role of the referrer. Hence we here say that user `anonymous` has referred us. 

> Note: In order to register an account, the registrar (here: myfunds) needs to be a **lifetime member**!

> Note: If you want to register the account of someone else, all you need is the **public key**.

    >>> register_account <name> <owner_pubkey> <active_pubkey> <registrar_account> <referrer_account> <referrer_percent> <broadcast>

**example** 

 We register a new user called mywallet, use the pubkey derived above and let our account myfunds pay the fee::

    >>> register_account mywallet BTS7D8jpQ2UwaQxqKyGpuhFQ9LBugCNxDhE7UN2jqgjVQgzG7zo9n BTS7D8jpQ2UwaQxqKyGpuhFQ9LBugCNxDhE7UN2jqgjVQgzG7zo9n myfunds anonymous 100 true

### register_account

|                     |  `register_account`  |
| ------------------- |---------- |
|  **description**    |  *Registers a third party’s account on the blockckain.*  <br/>This function is used to register an account for which you do not own the private keys. When acting as a registrar, an end user will generate their own private keys and send you the public keys. The registrar will use this function to register the account on behalf of the end user.   |
|                     |       |
|  **parameter**          |  **note**     |
| [name]         |  the name of the account, must be unique on the blockchain. Shorter names are more expensive to register; the rules are still in flux, but in general names of more than 8 characters with at least one digit will be cheap. (e.g., mywallet)   |                           
| [owner_pubkey]      | the owner key for the new account (e.g., "pub_key" value - created by the `suggest_brain_key`)   |                            
| [active_pubkey] | the active key for the new account  (e.g., "pub_key" value - created by the `suggest_brain_key`)|                           
| [register_account]  | the account which will pay the fee to register the user.  (e.g., myfunds)   |                            
| [referrer_account]  | the account who is acting as a referrer, and may receive a portion of the user’s transaction fees. (e.g., anonymous) | 
| [referrer_percentage]  | the percentage (0 - 100) of the new user’s transaction fees not claimed by the blockchain that will be distributed to the referrer. (e.g., 100)      |                            
| [broadcast]         | true to broadcast the transaction on the network. (e.g., true)      |


***
