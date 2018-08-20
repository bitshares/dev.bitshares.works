## Cli_Wallet and the Connectivity

#### Table of Contents:
- [BitShares Architecture – CLI Client and Wallet](#bitshares-architecture--cli-client-and-wallet)
- [Import a GUI-wallet account into CLI-wallet](../wallet/import_account.md#how-to-import-a-gui-wallet-account-into-cli-wallet) : How to, Often used Commands 
- [Connecting a Cli Wallet](../wallet/cli_wallet.md#connecting-a-cli-wallet)
- [Network and Wallet Configuration](../wallet/wallet_network.md#network-and-wallet-configuration) 
 :Components, General and Secure Network, Trusted Full Node, Delayed Full Node
- [A Collection of command lines - Node, Wallet, and Witness](../wallet/node_wallet_witness.md#a-collection-of-command-lines---node-wallet-and-witness) 
- FAQs: 
  - [Assets](../tutorials/FAQ.md#assets)
  - [Transfer/Transactions](../tutorials/FAQ.md#transfer--transactions)
  - [Wallet](../tutorials/FAQ.md#wallet--cli-wallet)


***

### BitShares Architecture – CLI Client and Wallet
- [CLI Client and Wallet (pdf)](../knowledge_base/shared_files/BitShares_Architecture-V3.pdf) 

![BitShares Architecture1 ](../../core/imgs/structure/bitshares-architecture-clinotop.png)

### cli_wallet

The Nodes are connected to the network and verify all transactions and block produces. The cli_wallet does not offer P2P or blockchain capabilities.  

Launching the cli_wallet requires a trusted Full Node to interact with the blockchain. You might use a localhost or own node to connect. Also, you can select one of [BitShares Public Full Nodes](https://github.com/bitshares/bitshares-ui/blob/staging/app/api/apiConfig.js#L67), or use API Server that runs by _businesses_ or _individuals_.

When you start the cli_wallet first time, it creates a local `wallet.json` file that contains the encrypted private keys. The key is required to access the funds and add new data to the blockchain with a signature from a private key.

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


