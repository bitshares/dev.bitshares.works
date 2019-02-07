
.. _cli-tool:

*******************************
Windows x64 CLI Tool
*******************************


.. contents:: Table of Contents
   :local: 

------------------

In this section, we will show you how to install cli-wallet tools for Windows and try several methods to get the data from blockchain. 

How to install BitShares-Core CLI Tool
===========================================================

CLI-Wallet on Windows (x64)
-------------------------------------------

1. Download the BitShares-Core-*-x64-cli-tools.zip file from https://github.com/bitshares/bitshares-core/releases
2. Unzip the file to your workstation. (e.g., d:\BitShares\)
3. Download the **cacer.pem** file from Mozilla: https://curl.haxx.se/docs/caextract.html
4. Save the file to the same folder you extracted the cli-tools. (e.g. d:\BitShares\)
5. Open a Windows command prompt (cmd.exe)
6. Navigate to your install directory. (e.g. d:\BitShares\)
7. Set an environment variable by typing: `set SSL_CERT_FILE=d:/BitShares/cacert.pem`
8. Start the wallet: `cli_wallet -s wss://{ANY VALID AUTHORITY}/ws`. For example, `cli_wallet -s wss://bitshares.openledger.info/ws`


-------

Start the CLI-Wallet (in Windows command prompt)
====================================================

**Note:** We used *the public API node of OpenLedger* and connect via secured websocket connection.


::

    >cli_wallet -s wss://bitshares.openledger.info/ws

If you open the cli-wallet successfully, you will see similar messages and receive ``new >>>`` prompt. Set a password and unlock the cli-wallte.::


    Logging RPC to file: logs\rpc\rpc.log
    2029115ms th_a       main.cpp:136                  main                 ] key_to_wif( committee_private_key ): 5KCBDTcyDqzsqehcb52tW5nU6pXife6V2rX9Yf7c3saYSzbDZ5W
    2029119ms th_a       main.cpp:140                  main                 ] nathan_pub_key: BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
    2029121ms th_a       main.cpp:141                  main                 ] key_to_wif( nathan_private_key ): 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
    Starting a new wallet with chain ID 4018d7844c78f6a6c41c6a552b898022310fc5dec06da467ee7905a8dad512c8 (from egenesis)
    2029128ms th_a       main.cpp:188                  main                 ] wdata.ws_server:   wss://bitshares.openledger.info/ws
    2029807ms th_a       main.cpp:193                  main                 ] wdata.ws_user:  wdata.ws_password:
    Please use the set_password method to initialize a new wallet before continuing
    new >>> 

- ``set_password``

(e.g.) we used `superaecret123` as a password.::

    new >>> set_password supersecret123
    set_password supersecret123
    null
    locked >>>

- ``unlock`` -  the cli-wallet::

    locked >>> unlock "supersecret123"
    unlock "supersecret123"
    null
    unlocked >>>

After this point, you can issue any command available to the cli-wallet (Wallet APIs) or construct your own transaction manually.

------

**Q. How to close the CLI wallet in a clean way**

- Although in Windows closing the whole window produces a nasty exception, you can use **ctrl-c** or **ctrl-d** to try to stops the process.

--------

Examples
=============

Several Calls you can try.

- [General](#general)
    - about
    - help
    - gethelp
- [Wallet Calls](#wallet-calls)
    - is_new
    - is_locked
- [Account Calls](#account-calls)
    - list_account_balances
    - get_account
    - get_account_id
- [Asset Calls](#asset-calls)
    - list_asset
    - get_asset


General
---------

- ``about``

::

    unlocked >>> about
    about
    {
      "client_version": "2.0.180328",
      "graphene_revision": "92eb45cbd3e61c163561b0e6cf7fc99c633e4fcf",
      "graphene_revision_age": "14 days ago",
      "fc_revision": "4441e1480778d8e98c4d0322b0e42b19752640b5",
      "fc_revision_age": "16 days ago",
      "compile_date": "compiled on Mar 28 2018 at 20:47:02",
      "boost_version": "1.57",
      "openssl_version": "OpenSSL 1.0.2l  25 May 2017",
      "build": "win32 64-bit"
    }

- ``about`` - _error_ (_*You don't need to add prentacises '()'._)

::

    unlocked >>> about ()
    about ()
    4 parse_error_exception: Parse Error
    Unexpected char '40' in ""
        {"c":40,"s":""}
        th_a  json_relaxed.hpp:675 fc::json_relaxed::variant_from_stream

        {"str":"about () "}
        th_a  json.cpp:474 fc::json::variants_from_string
    unlocked >>>

- ``help``

::

    unlocked >>> help
    help
        variant_object about()
        void add_operation_to_builder_transaction(transaction_handle_type transaction_handle, const operation & op)
        signed_transaction approve_proposal(const string & fee_paying_account, const string & proposal_id, const approval_delta & delta, bool broadcast)
        transaction_handle_type begin_builder_transaction()
        ...
        ...
        signed_transaction whitelist_account(string authorizing_account, string account_to_list, account_whitelist_operation::account_listing new_listing_status, bool broadcast)
        signed_transaction withdraw_vesting(string witness_name, string amount, string asset_symbol, bool broadcast)

    unlocked >>>

- ``gethelp``  - "list_accounts"

::

    unlocked >>> gethelp "list_accounts"
    
    Lists all accounts registered in the blockchain. This returns a list of all account names and their account ids, sorted by account name.
    Use the 'lowerbound' and limit parameters to page through the list. To retrieve all accounts, start by setting 'lowerbound' to the empty string '""', and then each iteration, pass the last account name returned as the 'lowerbound' for the next 'list_accounts()' call.
    
    Parameters:
        lowerbound: the name of the first account to return. If the named account does not exist, the list will start at the account that comes after 'lowerbound' (type: const string &) limit: the maximum number of accounts to return (max: 1000) (type: uint32_t)

    Returns
        a list of accounts mapping account names to account ids

    unlocked >>>
	

- ``gethelp``  - "save_wallet_file"

::

    unlocked >>> gethelp "save_wallet_file"
    gethelp "save_wallet_file"
    
    Saves the current wallet to the given filename.
    
    Parameters:
        wallet_filename: the filename of the new wallet JSON file to create or overwrite. If 'wallet_filename' is empty, save to the current filename. (type: string)

    unlocked >>>


Wallet Calls
----------------

-  ``is_new``

::

    unlocked >>> is_new
    is_new
    false
    unlocked >>>

- ``is_locked``

::

    unlocked >>> is_locked
    is_locked
    false
    unlocked >>>

	
Account Calls
-----------------

_ ``list_account_balances``  _(e.g. an existing user account "cedar036)_

::

    unlocked >>> list_account_balances "cedar036"
    list_account_balances "cedar036"
    75.22668 BTS
    3 OPEN.STEEM

    unlocked >>>

	
- ``get_acount``

::

    unlocked >>> get_account "cedar036"
    get_account "cedar036"
    {
      "id": "1.2.539269",
      "membership_expiration_date": "1970-01-01T00:00:00",
      "registrar": "1.2.96393",
      "referrer": "1.2.96393",
      "lifetime_referrer": "1.2.96393",
      "network_fee_percentage": 2000,
      "lifetime_referrer_fee_percentage": 3000,
      "referrer_rewards_percentage": 0,
      "name": "cedar036",
      "owner": {
        "weight_threshold": 1,
        "account_auths": [],
        "key_auths": [[
            "BTS7pRNvabZG2uU5SS9AyUWmsCdKijC1tHjCtiTDH7AeRkdH4qoVk",
            1
          ]
        ],
        "address_auths": []
      },
      "active": {
        "weight_threshold": 1,
        "account_auths": [],
        "key_auths": [[
            "BTS8jpGqVjRYytbguEZd4ao9CqdEUiMjkwfo9pgUE8RofW7higmQx",
            1
          ]
        ],
        "address_auths": []
      },
      "options": {
        "memo_key": "BTS8jpGqVjRYytbguEZd4ao9CqdEUiMjkwfo9pgUE8RofW7higmQx",
        "voting_account": "1.2.96393",
        "num_witness": 0,
        "num_committee": 0,
        "votes": [],
        "extensions": []
      },
      "statistics": "2.6.539269",
      "whitelisting_accounts": [],
      "blacklisting_accounts": [],
      "whitelisted_accounts": [],
      "blacklisted_accounts": [],
      "owner_special_authority": [
        0,{}
      ],
      "active_special_authority": [
        0,{}
      ],
      "top_n_control_flags": 0
    }
    unlocked >>>

	
- ``get_account_id``

::

    unlocked >>> get_account_id "cedar036"
    get_account_id "cedar036"
    "1.2.539269"
    unlocked >>>

- ``get_account_history``

::

    unlocked >>> get_account_history "cedar036" "4"
    get_account_history "cedar036" "4"
    2018-01-12T23:48:57 Transfer 40.46468 BTS from blocktrades to cedar036   (Fee: 0.01662 BTS)    
    2018-01-12T22:31:00 Transfer 3 OPEN.STEEM from tsugimoto0105 to cedar036   (Fee: 0.01662 BTS)
    2017-12-25T18:56:03 Transfer 0.00100 BTS from cedar036 to sharebits17 -- could not decrypt memo   (Fee: 0.23700 BTS)
    2017-12-25T17:44:15 Transfer 35 BTS from bitshares-users to cedar036   (Fee: 0.21851 BTS)

    unlocked >>>

- ``get_account_history`` - _error (*bad parameter format)_

::

    unlocked >>> get_account_history "cedar036, 7"
    get_account_history "cedar036, 7"
    10 assert_exception: Assert Exception
    a0 != e: too few arguments passed to method
        {}
        th_a  api_connection.hpp:169 fc::generic_api::call_generic
    unlocked >>>

Asset Calls
--------------

- list_assets::

    unlocked >>> list_assets "BTS" "2"
    list_assets "BTS" "2"
    [{
        "id": "1.3.0",
        "symbol": "BTS",
        "precision": 5,
        "issuer": "1.2.3",
        "options": {
          "max_supply": "360057050210207",
          "market_fee_percent": 0,
          "max_market_fee": "1000000000000000",
          "issuer_permissions": 0,
          "flags": 0,
          "core_exchange_rate": {
            "base": {
              "amount": 1,
              "asset_id": "1.3.0"
            },
            "quote": {
              "amount": 1,
              "asset_id": "1.3.0"
            }
          },
          "whitelist_authorities": [],
          "blacklist_authorities": [],
          "whitelist_markets": [],
          "blacklist_markets": [],
          "description": "",
          "extensions": []
        },
        "dynamic_asset_data_id": "2.3.0"
      },{
        "id": "1.3.368",
        "symbol": "BTS.SHARE",
        "precision": 4,
        "issuer": "1.2.31073",
        "options": {
          "max_supply": "100000000000000",
          "market_fee_percent": 0,
          "max_market_fee": "1000000000000000",
          "issuer_permissions": 79,
          "flags": 128,
          "core_exchange_rate": {
            "base": {
              "amount": 0,
              "asset_id": "1.3.0"
            },
            "quote": {
              "amount": 0,
              "asset_id": "1.3.0"
            }
          },
          "whitelist_authorities": [],
          "blacklist_authorities": [],
          "whitelist_markets": [],
          "blacklist_markets": [],
          "description": "share of btsbots",
          "extensions": []
        },
        "dynamic_asset_data_id": "2.3.368"
      }
    ]
    unlocked >>>

	
> Note: To list all assets, pass the empty string "" for the lowerbound to start at the beginning of the list, and iterate as necessary.

- ``get_asset``::

    unlocked >>> get_asset "BTS"
    get_asset "BTS"
    {
      "id": "1.3.0",
      "symbol": "BTS",
      "precision": 5,
      "issuer": "1.2.3",
      "options": {
        "max_supply": "360057050210207",
        "market_fee_percent": 0,
        "max_market_fee": "1000000000000000",
        "issuer_permissions": 0,
        "flags": 0,
        "core_exchange_rate": {
          "base": {
            "amount": 1,
            "asset_id": "1.3.0"
          },
          "quote": {
            "amount": 1,
            "asset_id": "1.3.0"
          }
        },
        "whitelist_authorities": [],
        "blacklist_authorities": [],
        "whitelist_markets": [],
        "blacklist_markets": [],
        "description": "",
        "extensions": []
      },
      "dynamic_asset_data_id": "2.3.0"
    }
    unlocked >>>

- ``get_asset`` - _error (*need to use capital letters)_

::

    unlocked >>> get_asset "bts"
    get_asset "bts"
    10 assert_exception: Assert Exception
    a:
        {}
        th_a  wallet.cpp:3076 graphene::wallet::wallet_api::get_asset
    unlocked >>>


|

|

