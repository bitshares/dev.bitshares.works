## Introduction & Architectures 

Welcome to BitShares! In this section, you will find the overview of BitShares Architecture (repositories and entities), and how the Core team processes the development work.  If you are interested in contributing to the Core team, you might want to check the Contribution Guide and the GitFlow to understand how BitShares-Core team handles the repository files and branches.  If you would like to know the Core team current project, issues, and the releases plans, check under the Issues and the Projects tabs.

### BitShares Architecture  
- [GitHub Repositories and Entities (pdf)](/core/knowledge_base/shared_files/BitShares_Architecture-V2b.pdf) 


### BitShares Core Workflow
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

#### [Installation:](/core/installation/README.md#install-a-development-environment)
BitShares offers you to install BitShares-Core into different platforms; Ubuntu (x64), OSx, and Windows. Check [this page](/core/installation/README.md#install-a-development-environment).

#### [BitShares Nodes information:](/core/nodes_full_witness/README.md#bitshares-nodes-and-p2p-network)
You should know before you start.

#### [Testnets: ](/core/testnets/README.md#testnets)

The above table list is a guide. It would change what you would like to do and try. 

**BitShares offer Public Testnet Web UI wallet.** If you want to check BitShares UI wallet functions, you might want to try out our Public Testnet Web Wallet. So, you can get better ideas before you start looking into the codes.

* [The Open Public Testnet information](/core/testnets/public_testnet_details.md#public-testnet-details) (include Public Testnet Web UI wallet link) 


***

