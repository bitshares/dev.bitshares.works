## Connecting a Cli Wallet

#### Table of Contents:
1. [Installation and build](../wallet/cli_wallet.md#1installation-and-build)
2. [Execute a cli_wallet](../wallet/cli_wallet.md#2-execute-a-cli_wallet)
3. [Unlock the cli_wallet](../wallet/cli_wallet.md#3unlock-the-cli_wallet)
4. [Gaining Access to Blockchain](../wallet/cli_wallet.md#4-gaining-access-to-blockchain)
5. [Creating Accounts](../wallet/cli_wallet.md#5-creating-accounts)
6. [Transferring Funds using the Cli-wallet](../wallet/cli_wallet.md#6-transferring-funds-using-the-cli-wallet)
7. [Obtain the private key](../wallet/cli_wallet.md#7-obtain-the-private-key)

***

### 1.Installation and build 
You have the option to obtain a `cli_wallet`.  
- 1a) Install and build it from BitShares-Core
  - BitShares offers you to install BitShares-Core into different platforms; Ubuntu (x64), OSx, and Windows.  Please refer [**Installation Guide **.](../installation/README.md#install-a-development-environment)
- 1b) Use the [Cli-Tools for Windows (option)](../wallet/cli_wallet.md#cli-tools-for-windows-option)
  - This option you do not need to install BitShates Core. To obtain the tool, go to a BitShares release page ( https://github.com/bitshares/bitshares-core/releases) and download the BitShares-Core-\*-x64-cli-tools.zip. 

### 2. Execute a cli_wallet

In this section, we show you two example patterns hoe to lunch the cli_wallet. The first one, we use the public API server node to connect the cli_wallet and also open WebSocket RPC or RPC-HTTP ports. The cli_wallet opens an RPC port for Wallet operations (i.e., spend, buy, sell...). The second one, we use an IP address (localhost) and also open the port for HTTP-RPC.

After connecting to the wallet, either way, you will get `>>> new` prompt. Then, we can do the same steps to proceed with this example.

#### \<Example1\> Connecting a Cli-Wallet by using the public API server node
We use the public API node of OpenLedger `wss://bitshares.openledger.info/ws` and connect via secured WebSocket connection:

    ./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws 
                                     -H 127.0.0.1:8092 
                                     -r 127.0.0.1:8093

This will open the cli-wallet and two RPC ports.  In order to allow RPC calls for wallet operations (spend, buy, sell, …), you need to specify the RPC port(s) and set a parameter to choose between WebSocket RPC `-r`  or RPC-HTTP '-H' requests. 

> **Note**: Websocket RPC via the `-r` parameter, and HTTP RPC via the `-H` parameter


#### \<Example2\> Connecting a Cli-Wallet by an IP address (localhost)

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090
                                     -H 127.0.0.1:8091
                                    
This will open the port 8091 for HTTP-RPC requests and has the capabilities to handle accounts (by Wallet API Calls) while the witness_node can only answer queries to the blockchain.

If you do not have a local wallet (`wallet.json` is a default file) yet, it will be created.

### 3.Unlock the cli_wallet

If you open the cli-wallet successfully, you will receive new >>> prompt. Set a password and unlock the cli-wallte.

    new >>>

**set_password**

(e.g.) we used `supersecretpassphrase` as a password. This password is used to encrypt the private keys in the wallet.

    new >>> set_password supersecretpassphrase
    set_password supersecretpassphrase
    null
    locked >>>

**unlock**

    locked >>> unlock "supersecretpassphrase"   
    unlocked >>>

> Note: After this point, you can issue any command available to the cli-wallet (Wallet APIs) or construct your own transaction manually.


**gethelp**

You can get more detailed information by issuing `gethelp`. Detailed explanations for most calls are available.

      unlocked >> gethelp <command>


### 4. Gaining Access to Blockchain

In Graphene, balances are contained in accounts. To claim an account that exists in the Graphene genesis, imports the private key for an existing account. The private key must match either an owner key or an active key for the named account.

**import_key**

    >>> import_key <name> "<wifkey>"

Funds are stored in genesis balance objects. These funds can be claimed, with no fee, by using the `import_balance` command. This call will construct transaction(s) that will claim all balances controled by wif_keys and deposit them into the given account.

**import_balance**

    >>> import_balance <name> ["*"] true


### 5. Creating Accounts

> **Note**: To register an account, the registrar needs to be a lifetime member. You can use `upgrade_account` to upgrade the account to *Lifetime member (LTM) status.

 We upgrade `faucet` account because `faucet` is the registrar in this example below.
 
    >>> upgrade_account faucet true

**register_account**

    >>> register_account <name> <owner-public_key> <active-public_key> <registrar_account> <referrer_account> <referrer_percent> <broadcast>

This command allows you to register an account using only a **public key**. 

\<Example\>

    >>> register_account alpha GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF faucet faucet 0 true

### 6. Transferring Funds using the Cli-wallet

In `transfer`, if the broadcast flag is `False`, the wallet will construct and sign, but **not**, broadcast the transaction. 

**transfer**

    unlocked >> transfer <from> <to> <amount> <asset> <memo> <broadcast>
   
\<Example\>  `faucet` wants to send 100000 `CORE` to `alpha` user.

    unlocked >> transfer faucet alpha 100000 CORE "here is the cash" true

The wallet will return the actually signed transaction.

> **Note**: Many calls have an obligatory `broadcast` -flag as the last argument. If this flag is `False`, the wallet will construct and sign but **not** broadcast the transaction. This can be very useful for a local storage setup or to verify transactions.


\<Example\>  Open a new Wallet for `alpha` user

    >>> import_key alpha 5HuCDiMeESd86xrRvTbexLjkVg2BEoKrb7BAA5RLgXizkgV3shs

    >>> upgrade_account alpha true

    >>> create_witness alpha "http://www.alpha" true



### 7. Obtain the private key

The `get_private_key` command allows us to obtain the **private key** corresponding to the block signing key.

    >>> get_private_key(<pubkey>) 
   

    >>> get_private_key GPH6viEhYCQr8xKP3Vj8wfHh6WfZeJK7H9uhLPDYWLGCRSj5kHQZM


***
