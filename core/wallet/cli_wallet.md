## Connecting a Wallet 

#### Contents:

- [Overview](#overview)
- [Cli-Tools for Windows (option)](#cli-tools-for-windows-option)
- [Create a Cli Wallet and Open RPC port](#create-a-cli-wallet-and-open-rpc-port)
   - [Case 1: Connecting a Cli-Wallet - use the public API node](#case-1-connecting-a-cli-wallet)
   - [Case 2: Connecting a Cli-Wallet](#case-2-connecting-a-cli-wallet)
   - [Case 3: Connecting a Cli-Wallet - Public Testnet](#case-3-connecting-a-cli-wallet-in-public-testnet)
- [Gaining a Access to Blockchain](#gaining-access-to-blockchain)
   - [import](#import)
   - [register](#register)
- [Transferring  Funds using the Cli-wallet](#transferring-funds-using-the-cli-wallet)

******


### Overview:

The Cli_wallet creates a local _wallet.json_ file that contains the encrypted private keys. The key is required to access the funds and add new data to the blockchain. You will need to `unlock` the wallet before you start interacting with the blockchain. It requires a running witness node (API server) to interface with the blockchain.

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


**Reminder**: To register an account, the registrar needs to be a lifetime member. You can upgrade the account to *Lifetime member (LTM) status.* (e.g. `faucet` is the registrar in an example below)

    >>> upgrade_account faucet true

***

## Cli-Tools for Windows (option)

You might have an option to use `cli tools` for Windows to start a cli-wallet. You do not need to install BitShates Core project. If you are interested, check a BitShares release page ( https://github.com/bitshares/bitshares-core/releases) and download the BitShares-Core-*-x64-cli-tools.zip. Here is an instruction [link.](/source/1_installation/1-4_windows_cli_tools.md#contents)

****

## Create a Cli Wallet and Open RPC port

We assume you have...
   - Successfully installed BitShares Core
   - Successfully build ($make)

And  compile the cli wallet manually via, 

     $ make cli_wallet 

### Requirements 

It requires a running witness node (not necessarily locally) and connects to it.

Your running witness/full node might be _BitShares Public Full Nodes_, or run by _businesses_ or _individuals_.

### After opened the port

You will be prompted: `locked >>>`. You need to unlock the wallet. After the `unlock`, you can issue any command available to the cli-wallet (Wallet APIs) or construct your own transaction manually. For the trading, the wallet needs to be unlocked sign trading orders. 

----

- Here is a latest list of [Public Full Node](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67) information

## Case 1: Connecting a Cli-Wallet

We use the public API node of OpenLedger `wss://bitshares.openledger.info/ws` and connect via secured websocket connection:

**Create a Wallet**

    ./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws

This will **open the cli-wallet** and unless you already have a local wallet, and will ask you to provide a password for your local wallet. Once a wallet has been created (default wallet file is *wallet.json*), you will receive,

    new >>>

**set_password**

(e.g.) we used `supersecretpassphrase` as a password.

    new >>> set_password supersecretpassphrase
    set_password supersecretpassphrase
    null
    locked >>>

The wallet can be unlocked by providing the password.

**unlock**

    locked >>> unlock "supersecretpassphrase"
    
    unlocked >>>

> Note: After this point, you can issue any command available to the cli-wallet (Wallet APIs) or construct your own transaction manually.

### Opening Remote Procedure Call (RPC) Port

In order to allow RPC calls for wallet operations (spend, buy, sell, …) you can choose between pure RPC or RPC-HTTP requests.

* websocket RPC via the `-r` parameter, and
* HTTP RPC via the `-H` parameter:

In our example below, it shows that the cli-wallet opens two RPC ports. You can interface your application with it.

    ./programs/cli_wallet/cli_wallet -s wss://bitshares.openledger.info/ws -H 127.0.0.1:8092 -r 127.0.0.1:8093

The command above will open the cli-wallet and, unless you already have a local wallet, will ask you to provide a password for your local wallet. Once a wallet has been created (default wallet file is `wallet.json`), it will prompt with `locked >>>`.

***

## Case 2: Connecting a Cli-Wallet


**Create a Wallet**

    ./programs/cli_wallet/cli_wallet -s ws://127.0.0.1:8090

Depending on the actual chain that you want to connect to your may need to specify –chain-id.

### Opening Remote Procedure Calls (RPC) Port

In order to allow RPC calls for wallet operations (spend, buy, sell, …) you can choose between pure RPC or RPC-HTTP requests. In this example, the latter is preferred since well established libraries make use of the RPC-HTTP protocol.

The cli-wallet can open a RPC port so that you can interface your application with it. 

**To enable RPC-HTTP in your wallet you need to run:**

    # recommended for use with python, or curl:
    ./programs/cli_wallet/cli_wallet --rpc-http-endpoint="127.0.0.1:8092"
    # or
    ./programs/cli_wallet/cli_wallet --rpc-endpoint="127.0.0.1:8092"

depending on the kind of RPC protocol.

* websocket RPC via the `-r` parameter
* HTTP RPC via the `-H` parameter


**This example will open the port 8092 for local queries only. It is not recommended to publicly expose your wallet!**

***

## Case 3: Connecting a Cli-Wallet in Public Testnet

We will now show how to connect a cli-wallet to the new blockchain and generate our first transaction on the new blockchain.

In order to create a wallet (on Public Testnet), you must specify the previously setup server. With the witness node’s default access control settings.


**Create a Wallet - Public Testnet**

    ./programs/cli_wallet/cli_wallet --wallet-file my-wallet.json -s ws://127.0.0.1:11011 -H 127.0.0.1:8090 -r 127.0.0.1:8099

> Note: The parameter `-H` is required so that we can interface with the cli-wallet via RPC-HTTP-JSON, later while -r will open a port for the Ruby-based faucet.

If you get the `set_password` prompt, it means your CLI has successfully conected to the testnet witness node.

> To use the wallet, you must `unlock` the wallet.  

***

## Gaining Access to Blockchain

In Graphene, balances are contained in accounts. To import an account that exists in the Graphene genesis. (i.g.,\<name\> is an account name owning the key.  \<wifkey\> is a private key)

### Import

**import_key**

    >>> import_key <name> "<wifkey>"

Funds are stored in genesis balance objects. These funds can be claimed, with no fee.

**import_balance**

    >>> import_balance <name> ["*"] true

### Register
To register an account, the registrar needs to be a lifetime member. You can upgrade the account to *Lifetime member (LTM) status.* (e.g. `faucet` is the registrar in an example below)

    >>> upgrade_account faucet true

**register Account**

    >>> register_account <name> <owner-public_key> <active-public_key> <registrar_account> <referrer_account> <referrer_percent> <broadcast>

This command allows you to register an account using only a **public key**. 

*Example*

    >>> register_account alpha GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF GPH4zSJHx7D84T1j6HQ7keXWdtabBBWJxvfJw72XmEyqmgdoo1njF faucet faucet 0 true

### Transferring Funds using the Cli-wallet

In `transfer`, if the broadcast flag is `False`, the wallet will construct and sign, but **not** broadcast the transaction. This can be very useful for a cold storage setup or to verify transactions.

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

**Obtain the private key**

The `get_private_key` command allows us to obtain the **private key** corresponding to the block signing key.
(*Get the WIF private key corresponding to a public key. The private key must already be in the wallet.* )

    >>> get_private_key(<pubkey>) 
   

    >>> get_private_key GPH6viEhYCQr8xKP3Vj8wfHh6WfZeJK7H9uhLPDYWLGCRSj5kHQZM

***

**Open a new Wallet for `alpha` user**

    >>> import_key alpha 5HuCDiMeESd86xrRvTbexLjkVg2BEoKrb7BAA5RLgXizkgV3shs

    >>> upgrade_account alpha true

    >>> create_witness alpha "http://www.alpha" true

***


