# Introduction & Architectures 

#### Table of Contents:
- [BitShares Members](#bitshares-members)
- [BitShares Architecture  ](#bitshares-architecture--github-repositories-and-entities)
- [Development Environments and Requirements](#development-environments-and-requirements)
  - [BitShares Code and Files](#bitshares-code-and-files)
- [Features](#bitshares-key-design-concepts-and-the-features)
- [BitShares Blockchain Transactions and Performance](#observe-bitshares-blockchain-transactions-and-performance)
- [BitShares Core](#bitshares-core-projects-guide)
  - [Projects Guide](#bitshares-core-projects-guide)
  - [Code Management - GitFlow](#bitshares-core-code-management---gitflow)
- [Your purpose and a download branch](#your-purpose-and-a-download-branch)

***

Welcome to BitShares! In this section, you will find the overview of BitShares Architecture (repositories and entities), and how the Core team processes the development work.  If you are interested in contributing to the Core team, you might want to check the Contribution Guide and the GitFlow to understand how BitShares-Core team handles the repository files and branches.  If you would like to know the Core team current project, issues, and the releases plans, check under the Issues and the Projects tabs.

# BitShares Members 
If you have an BitShares account, you are a BTS Holder and you have a voting power to make decisions in the BitShares Community.

![BitShares Architecture](../imgs/structure/btsholders-v1.png)

# BitShares Architecture – GitHub Repositories and Entities
- [GitHub Repositories and Entities with a guide (pdf)](../knowledge_base/shared_files/BitShares_Architecture-V3.pdf) 

![BitShares Architecture](../imgs/structure/bitshares-architecture-v3notop.png)


# Development Environments and Requirements

BitShares offers you to install BitShares-Core into different platforms; [**Linux:Ubuntu (x64)**](../installation/build_ubuntu.md#building-on-ubuntu), [**OS X**](../installation/build_osx.md#building-on-os-x), and [**Windows**](../installation/build_windows.md#building-on-windows)**.  There are  dependencies to check when you download OpenSSL and  Boost. Please make sure which versions you downloaded. 

Also, if you are a Windows user, you have other two choices to install BitShares Core to your Windows (x64) Operation System.  One is [**CLI-Wallet tools for Windows (x64)**](../installation/windows_cli_tool.md#cli-wallet-on-windows-x64), another is [**Windows SubSystem for Linux (WSL)**](../installation/wsl.md#windows-subsystem-for-linux-wsl) .  

The CLI-wallet tools for Windows (x64) allows you to try several methods to get the data from BitShares blockchain without the BitShares Core installation. After you download the cli-wallet tools, you will find a cli_wallet executable to try Wallet API. 

Another option, Windows SubSystem for Linux (WSL).  This is for a developer who uses a Windows 10 (x64) Operation System and wants to build BitShares Core on Ubuntu. 

> See [System Requirements](../nodes_full_witness/full_nodes.md#system-requirements) if you are interested to run a node (updated: 2018-07-02).

# BitShares Code and Files 

- Open Source program
- Language uses (mainly): BitShares-Core(C++), python
- [BitShares GitHub](https://github.com/bitshares)
  - **BitShares-Core (C++)** - BitShares Blockchain implementation and command-line interface.
  - **Bitshares-FC** - Fast-compiling C++ library 
  - **BitShares python** - Fully featured client-side library for the BitShares Blockchain - written entirely in python.
  - **BitShares-UI** - Fully featured Graphical User Interface / Reference Wallet for the BitShares Blockchain.
  - **BSIPs** - BitShares Improvement Proposals and Protocols. These technical documents describe the process of updating and improving the BitShares blockchain and technical ecosystem.
  - **BitSharesjs** - JavaScript tools for BitShares Encryption and Serialization.
  - **BitSharesjs-ws** - Javascript websocket interface for Bitshares 
  - more

# BitShares key Design Concepts and the Features

BitShares is a Finance Smart contract platform supported by Graphene technology - Blockchain.  
BitShares is designed from the ground up to process more transactions every second than VISA and MasterCard combined.

#### Key points and fundamentals for the design:
- Keep everything in memory.
- Keep the core business logic in a single thread.
- Keep cryptographic operations (hashes and signatures) out of the core business logic.
- Divide validation into state-dependent and state-independent checks.
- Use an object-oriented data model.
- Avoid synchronization primitives (locks, atomic operations)
- Minimize unnecessary computation in the business logic processor.
    
BitShares is built to aim high-performance blockchain and has been done to remove all calculations that are not part of the critical, order-dependent, evaluation from the core business logic, and to design a protocol the facilitates these kinds of optimizations.

### BitShares Available Features
BitShares can be made to function as a software, a network, a ledger, a bank, an exchange, and a currency all at once. (e.g., It can fulfill the role of a bank by maintaining a distributed ledger that tracks debt collateralized by other assets. You can find out that BitShares offers numerous features that are not available on other popular blockchain platforms.

- **SmartCoins** are fungible, divisible and free from any restrictions. A SmartCoin is a cryptocurrency whose value is pegged to that of another asset, such as the US Dollar or gold. SmartCoins implement the concept of a collateralized loan and offer it on the blockchain.
- **Decentralized Exchange** - BitShares provides a high-performance decentralized exchange, with all the features you would expect in a trading platform. 
  - Secure: All of the reserves are kept as BTS held on the blockchain, and they cannot be stolen, because there are no private keys that can be compromised to steal the reserves.
- Trading / Financial Services 
- Transferable Named Account (human-friendly account name)
- Globally unique account name and ID.
- Dynamic Account Permissions
- Multi-user control for account
- Two authorities: owner and active keys
- Transaction + multi-signature authority
- **Proposed transaction infrastructure** - witch tracks partially approved transactions.
  - It can be used for a scheduled payment
-Fees calculation
  - Transaction fee
  - Fee Schedules
- Assets
  - **User Issues Asset (UIA)** - to help facilitate profitable business models for certain types of services.
   - *Use Cases* (Event tickets, Reward points, privatized SmartCoins, Predictions Market, more)
    - How to profit? -> Fee pools
- BitAsset - BitUSD,       
- **Delegated Proof of State Consensus (DPOS)** - Under DPOS, BTS Holder has influence...
   - A robust and flexible consensus protocol.
 - Block Production by Elected witnesses
 - Referral Program
 - Vesting valance

# Observe BitShares Blockchain Transactions and Performance

#### Bitshares Block Explorer
BitShares Explorer shows BitShares Blockchain information. You can observe BitShares Blockchain *Health* Status (head_block_num, head_block_age, chain_id, etc.), how transactions processing, assets volume, and members.

If you would like to see more detailed information, the BitShares Open Explorer offers other information tabs (i.e., Operations, Proxies, Markets, SmartCoins, UIAs, and Holders) to view.

- [Cryptofresh](https://www.cryptofresh.com/)
- [Open Explorer](http://open-explorer.io/)
- [bts.ai](https://bts.ai/)

#### Blockchain Activity
Blockchain Activity Matrix shows different tokens (assets) *Activity*, *Value*, and *Index*. It's interesting to check the CUI index is Capacity Utilization Index (a rate of blockchain daily activity to total blockchain capacity) to see how much numbers BitShares has.

- http://blocktivity.info/
  
# BitShares Core Projects Guide

### Project Guide
- [Contribution Guide](../intro/contribution_guide.md#contribution-guide) [DRAFT]
- [Contribution Guide Project](https://github.com/bitshares/bitshares-core/projects/6)

**BitShares-Core (Team) :**
This is a team of developers who manage BitShares-Core code updates and handle the issues that are submitted by developers. The team creates a project plan of next release and delivers to the Bitshares community about the Project information. 
  - **Roles** 
    - improving
    - maintaining
    - upgrading protocol if needed
    - making Project plans for the future release
    - creating/ announcing  Release 
    
### BitShares Core Code Management - GitFlow
This section describes and defines how changes flow into our code and through the various stages of development until it finally goes into production.

- [Purpose](../intro/bitshares_core_gitflow.md)
- [Non-Consensus: Development / Release / Bugfix Workflows](../intro/bitshares_core_gitflow.md#non-consensus-development--release--bugfix-workflows)
- [Consensus: Development / Release / Bugfix Workflows](../intro/bitshares_core_gitflow.md#consensus-development--release--bugfix-workflows)
- [Goals To Achieve](../intro/bitshares_core_gitflow.md#goals-to-achieve)
- [Basic Rules](../intro/bitshares_core_gitflow.md#basic-rules)
- [How To Create a Release](../intro/bitshares_core_gitflow.md#how-to-create-a-release)
- [How To Create an Emergency Fix](../intro/bitshares_core_gitflow.md#how-to-create-an-emergency-fix)
- [Emergency Fix Workflows](../intro/bitshares_core_gitflow.md#emergency-fix-workflows)

***

### Your purpose and a download branch
The below table list is just a guide. If you are interested to learn BitShares Node or CLI wallet, using TestNet might be adequate to start. If you are a developer who is interested to contribute to the BitShares Core team, downloading a developer branch might be a good start. Select an appropriate branch when you install BitShares-Core.  

After you know which branch to download, your next step is the installation of BitShares-Core. Check the [BitShares Installation Guide](../installation/README.md#install-a-development-environment) and select your Operation System to follow the installation steps. 

Your purpose might be trying some API calls to query the BitShares Blockchain data, using a CLI Wallet to manage your assets, or running a node. In any events, you will create an account and interact with the blockchain. If you install BitShares-Core, it would be a better understanding of the BitShares Node and System Requirements. So, we recommend you to read [the BitShares Nodes](../nodes_full_witness/README.md#bitshares-nodes-and-p2p-network) section and [BitShares Accounts](../accounts/README.md#bitshares-accounts) section before the installation. 


|   purpose      | Extract only |Tx+not modify code| Tx+modify code | Develop branch | Release branch |Testnet: may use|
| ------------------- |:----------:|:---:|:---:|:---:|:---:|:---:|
| Only Extract Data from the Blockcahin |[X] |  |  |  | install | public|
| Want to use BitShares Blockchain for business | | [x] |  |  | install |(public) |
| Want to help the Core Team          | |  | [x] |install  |  |public |
| Fork: experiment with changes, etc  | |  | [x] | Entire Core repo | Entire Core repo |private |
| Want to develop own application     | |  | [x] |  | install |private |
| Want to build tools for trading     | |[x] | |  | install |private |
| Want to create a custom blockchain  | |  | [x] |  | install |private |


***


