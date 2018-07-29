## Cli_Wallet and the Connectivity

#### Table of Contents:
- [BitShares Architecture – CLI Client and Wallet](/core/wallet/README.md#bitshares-architecture--cli-client-and-wallet)
- [Connecting a Cli Wallet](/core/wallet/cli_wallet.md#connecting-a-cli-wallet)
- [Network and Wallet Configuration](/core/wallet/wallet_network.md#network-and-wallet-configuration)
- [A Collection of command lines - Node, Wallet, and Witness](/core/wallet/node_wallet_witness.md#a-collection-of-command-lines---node-wallet-and-witness)
- FAQs: [Assets](/core/wallet/README.md#faq---assets), [Transfer/Transactions](/core/wallet/README.md#faq---transfer--transactions), [Wallet](/core/wallet/README.md#faq---wallet)

***

### BitShares Architecture – CLI Client and Wallet
- [CLI Client and Wallet (pdf)](/core/knowledge_base/shared_files/BitShares_Architecture-V3.pdf) 

<p align="center">
  <img src="/core/imgs/structure/bitshares-architecture-clinotop.png" width="830" title="BitShares Architecture ">
</p>



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

### [Connecting a Cli Wallet](/core/wallet/cli_wallet.md#connecting-a-cli-wallet)
1. [Installation and build](/core/wallet/cli_wallet.md#1installation-and-build)
2. [Execute a cli_wallet](/core/wallet/cli_wallet.md#2-execute-a-cli_wallet)
3. [Unlock the cli_wallet](/core/wallet/cli_wallet.md#3unlock-the-cli_wallet)
4. [Gaining Access to Blockchain](/core/wallet/cli_wallet.md#4-gaining-access-to-blockchain)
5. [Creating Accounts](/core/wallet/cli_wallet.md#5-creating-accounts)
6. [Transferring Funds using the Cli-wallet](/core/wallet/cli_wallet.md#6-transferring-funds-using-the-cli-wallet)
7. [Obtain the private key](/core/wallet/cli_wallet.md#7-obtain-the-private-key)


### [Network and Wallet Configuration](/core/wallet/wallet_network.md#network-and-wallet-configuration)
   - [Components](/core/wallet/wallet_network.md##components)
      - P2P Network
      - Trusted Full Node
      - Wallet
      - Wallet API     
   - [General Network and Wallet Configuration](/core/wallet/wallet_network.md#general-network-and-wallet-configuration)
      - Trusted Full Node
      - Wallet
   - [Secure Network and Wallet Configuration](/core/wallet/wallet_network.md#secure-network-and-wallet-configuration)
      - Trusted Full Node
      - Delayed Full Node
      - Wallet

### [A Collection of command lines - Node, Wallet, and Witness](/core/wallet/node_wallet_witness.md#a-collection-of-command-lines---node-wallet-and-witness)

- [Wallet Full Nodes & Witness Nodes](/core/wallet/wallet_full_nodes_witness_nodes.md#wallet-full-nodes--witness-nodes)
   - Examples - connect to the network and open up a node


### FAQ - Assets

- [How to Create a new UIA](/core/tutorials/assets_uia.md#how-to-create-a-new-uia)
- [more...](/core/tutorials/FAQ.md#assets)

### FAQ - Transfer / Transactions
- [How to use the CLI wallet to perform confidential transfers in BitShares](/core/tutorials/trn_confidential_transfer.md#how-to-use-the-cli-wallet-to-perform-confidential-transfers-in-bitshares)
- [more...](/core/tutorials/FAQ.md#transfer--transactions)

### FAQ - Wallet
- [How can I close the CLI client in a clean way?](/core/tutorials/FAQ.md#q-how-can-i-close-the-cli-client-in-a-clean-way)
- [How can I import my GUI-wallet account into CLI-wallet?](/core/tutorials/FAQ.md#q-how-can-i-import-my-gui-wallet-account-into-cli-wallet)
- [Why does the CLI client crash immediately when I try to run it for the first time?](/core/tutorials/FAQ.md#q-why-does-the-cli-client-crash-immediately-when-i-try-to-run-it-for-the-first-time)
- [more...](/core/tutorials/FAQ.md#wallet--cli-wallet)

***


