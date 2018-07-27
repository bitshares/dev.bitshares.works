## Introduction & Architectures 

Welcome to BitShares! In this section, you will find the overview of BitShares Architecture (repositories and entities), and how the Core team processes the development work.  If you are interested in contributing to the Core team, you might want to check the Contribution Guide and the GitFlow to understand how BitShares-Core team handles the repository files and branches.  If you would like to know the Core team current project, issues, and the releases plans, check under the Issues and the Projects tabs.

### BitShares Architecture  
- [GitHub Repositories and Entities (pdf)](/core/knowledge_base/shared_files/BitShares_Architecture-V3.pdf) 


### BitShares Core 
- [Contribution Guide](/core/intro/contribution_guide.md#contribution-guide) [DRAFT]
- [Contribution Guide Project](https://github.com/bitshares/bitshares-core/projects/6)

### GitFlow - Code Management

- [Purpose](/core/intro/bitshares_core_gitflow.md)
- [Non-Consensus: Development / Release / Bugfix Workflows](/core/intro/bitshares_core_gitflow.md#non-consensus-development--release--bugfix-workflows)
- [Consensus: Development / Release / Bugfix Workflows](/core/intro/bitshares_core_gitflow.md#consensus-development--release--bugfix-workflows)
- [Goals To Achieve](/core/intro/bitshares_core_gitflow.md#goals-to-achieve)
- [Basic Rules](/core/intro/bitshares_core_gitflow.md#basic-rules)
- [How To Create a Release](/core/intro/bitshares_core_gitflow.md#how-to-create-a-release)
- [How To Create an Emergency Fix](/core/intro/bitshares_core_gitflow.md#how-to-create-an-emergency-fix)
- [Emergency Fix Workflows](/core/intro/bitshares_core_gitflow.md#emergency-fix-workflows)

***

### Your purpose:


|   purpose      | Extract only |Tx+not modify code| Tx+modify code | Develop branch | Release branch |Testnet: may use|
| ------------------- |:----------:|:---:|:---:|:---:|:---:|:---:|
| Only Extract Data from the Blockcahin |[X] |  |  |  | install | public|
| Want to use BitShares Blockchain for business | | [x] |  |  | install |(public) |
| Want to help the Core Team          | |  | [x] |install  |  |public |
| Fork: experiment with changes, etc  | |  | [x] | Entire Core repo | Entire Core repo |private |
| Want to develop own application     | |  | [x] |  | install |private |
| Want to build tools for trading     | |[x] | |  | install |private |
| Want to create a custom blockchain  | |  | [x] |  | install |private |

The above table list is a guide. If you are interested to learn BitShares Node or CLI wallet, using TestNet might be adequate to start. If you are a developer who is interested to contribute to the BitShares Core team, downloading a developer branch might be a good start. Select an appropriate branch when you install BitShares-Core.  

After you know which branch to download, your next step is the installation of BitShares-Core. Check the BitShares Installation Guide and select your Operation System to follow the steps. 

Your purpose might be trying some API calls to query the BitShares Blockchain data, using a CLI Wallet to manage your assets, or running a node. In any events, you should have a better understanding of the BitShares Node and System Requirements. So, we recommend you to read [the BitShares Nodes](/core/nodes_full_witness/README.md#bitshares-nodes-and-p2p-network) section before the installation. 



***

