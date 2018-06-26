## Connecting a Wallet 

#### Table of Contents:

- [Overview](#overview)
- [Cli-Tools for Windows (option)](#cli-tools-for-windows-option)
- [Create a Cli Wallet and Open RPC port](/core/wallet/cli_wallet.md#create-a-cli-wallet-and-open-a-remote-procedure-call-rpc-port)
   - [Case 1: Connecting a Cli-Wallet - the public API server node](/core/wallet/cli_wallet.md#case-1-connecting-a-cli-wallet---the-public-api-server-node)
   - [Case 2: Connecting a Cli-Wallet - a Node (IP address)](/core/wallet/cli_wallet.md#case-connecting-a-cli-wallet---a-node-ip-address)
- [Unlock the wallet](/core/wallet/cli_wallet.md#unlock-the-wallet)
- [Gaining a Access to Blockchain](/core/wallet/cli_wallet_2.md#gaining-access-to-blockchain)
   - [import_key and import_balance](/core/wallet/cli_wallet_2.md#gaining-access-to-blockchain)
- [Register_account](/core/wallet/cli_wallet_2.md#register)
- [Transferring  Funds using the Cli-wallet](#transferring-funds-using-the-cli-wallet)
- [Obtain the private key](/core/wallet/cli_wallet_2.md#obtain-the-private-key)

******


### Overview:

The Nodes are connected to the network and verify all transactions and block produces. The cli_wallet is used to initiate transfer and connects to the trusted full node. 

The cli_wallet creates a local [`wallet.json`](/core/wallet/cli_wallet.md#overview) file that contains the encrypted private keys. The key is required to access the funds and add new data to the blockchain with a signature from a private key. 

Connecting the cli_wallet requires a running full node (not necessarily locally) and connect to it. You might have own node to use. If you do not have it, you can select one of [BitShares Public Full Nodes](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67), or nodes that run by _businesses_ or _individuals_.

**Example: wallet.json**

      {
        "chain_id": "4018d7844c78f6a6c41c6a552b898022310fc5dec06da4222222",
        "my_accounts": [],
        "cipher_keys": "4144454a976266ed15f736df4f5645e60bace86eb87cb5b59b0c8f48b75c6131167807c403a56060528b7dae993de667736d5ab9ef1f60fb340c4aa70437ec7a2534bbdab051b9d2d1871111111",
        "extra_keys": [],
        "pending_account_registrations": [],
        "pending_witness_registrations": [],
        "labeled_keys": [],
        "blind_receipts": [],
        "ws_server": "ws://localhost:8090",
        "ws_user": "",
        "ws_password": ""
      }



***

### Cli-Tools for Windows (option)

You have an option to use `cli tools` for Windows to start the cli-wallet. You do not need to install BitShates Core project. If you are interested, check a BitShares release page ( https://github.com/bitshares/bitshares-core/releases) and download the BitShares-Core-*-x64-cli-tools.zip. Here is an instruction [link.](/source/1_installation/1-4_windows_cli_tools.md#contents)

****

### Create a Cli Wallet and Open a Remote Procedure Call (RPC) Port

We assume you have...
   - Successfully installed BitShares Core
   - Successfully Build or compile (cli_wallet)
   

In this dection, we show you two example patterns of the command lines. The first one, we use the public API server node to connect the cli_wallet and also open Websocket RPC or RPC-HTTP ports. The cli_wallet opens a RPC port for Wallet operations (i.e., spend, buy, sel...).  The second one, we use an IP address (localhost) and also open the port for HTTP-RPC. 

After connect to the wallet either way, you will get `>>> new`. Then, we can do the same steps to proceed this example.

In order to allow RPC calls for wallet operations (spend, buy, sell, …) you can choose between pure RPC or RPC-HTTP requests.
The cli-wallet can open a RPC port so that you can interface your application with it. 

----

### Case 1: Connecting a Cli-Wallet - the public API server node

We use the public API node of OpenLedger `wss://bitshares.openledger.info/ws` and connect via secured websocket connection:

**Connecting a Wallet and Opening RPC Ports**

    ./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws 
                                     -H 127.0.0.1:8092 
                                     -r 127.0.0.1:8093

This will open the cli-wallet. In order to allow RPC calls for wallet operations (spend, buy, sell, …), you open the RPC ports and set a parameter to choose between Websocket RPC or RPC-HTTP requests. In our example above, it shows that the cli-wallet opens two RPC ports also. You can interface your application with it.

> **Note**: Websocket RPC via the `-r` parameter, and HTTP RPC via the `-H` parameter

***

### Case 2: Connecting a Cli-Wallet - a Node (IP address)

**Connecting a Wallet and Opening RPC Port**

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090
                                     -H 127.0.0.1:8091

This will open the port 8091 for HTTP-RPC requests and has the capabilities to handle accounts while the witness_node can only answer queries to the blockchain.

*It is not recommended to publicly expose your wallet!*

***

#### After coneccted the cli_wallet and opened the RPC port

Unless you already have a local wallet, it will ask you to provide a password for your local wallet. Once a wallet has been created (default wallet file is *wallet.json*), you will receive,

    new >>>

### Unlock the wallet

**set_password**

(e.g.) we used `supersecretpassphrase` as a password.

    new >>> set_password supersecretpassphrase
    set_password supersecretpassphrase
    null
    locked >>>

**unlock**

    locked >>> unlock "supersecretpassphrase"   
    unlocked >>>

> Note: After this point, you can issue any command available to the cli-wallet (Wallet APIs) or construct your own transaction manually.


### Gaining Access to Blockchain

In Graphene, balances are contained in accounts. To claim an account that exists in the Graphene genesis, use the `import_key` command.

**import_key**

    >>> import_key <name> "<wifkey>"

Funds are stored in genesis balance objects. These funds can be claimed, with no fee, by using the `import_balance` command.

**import_balance**

    >>> import_balance <name> ["*"] true

### Register

> **Note**: To register an account, the registrar needs to be a lifetime member. You can upgrade the account to *Lifetime member (LTM) status.

e.g. We upgrade `faucet` account because `faucet` is the registrar in an example below.

    >>> upgrade_account faucet true

**register Account**

    >>> register_account <name> <owner-public_key> <active-public_key> <registrar_account> <referrer_account> <referrer_percent> <broadcast>

This command allows you to register an account using only a **public key**. 

*Example*

    >>> register_account alpha GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF faucet faucet 0 true

### Transferring Funds using the Cli-wallet

In `transfer`, if the broadcast flag is `False`, the wallet will construct and sign, but **not**, broadcast the transaction. 

**transfer**

    unlocked >> transfer <from> <to> <amount> <asset> <memo> <broadcast>
    
*(Example 1):*
 `faucet` wants to send 100000 `CORE` to `alpha` user.

    unlocked >> transfer faucet alpha 100000 CORE "here is the cash" true

*(Example 2):*
`Alice` wants to send 10 `USD` to `bob`.

    unlocked >> transfer alice bob 10 USD "a gift" true

The wallet will return the actual signed transaction.

***

### Obtain the private key

The `get_private_key` command allows us to obtain the **private key** corresponding to the block signing key.

    >>> get_private_key(<pubkey>) 
   

    >>> get_private_key GPH6viEhYCQr8xKP3Vj8wfHh6WfZeJK7H9uhLPDYWLGCRSj5kHQZM

***

*(Example):*
**Open a new Wallet for `alpha` user**

    >>> import_key alpha 5HuCDiMeESd86xrRvTbexLjkVg2BEoKrb7BAA5RLgXizkgV3shs

    >>> upgrade_account alpha true

    >>> create_witness alpha "http://www.alpha" true

***

