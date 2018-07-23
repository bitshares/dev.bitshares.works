## Introduction & Architectures 

### BitShares Architecture  
- [GitHub Repositories and Entities](/core/knowledge_base/shared_files/BitShares_Architecture-V2b.pdf) 


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


#### [Testnets: ](/core/testnets/README.md#testnets)

The above list is a guide. It would change what you would like to do and try. 

**BitShares offer Public Testnet Web UI wallet.** You might want to try out our Public Testnet Web wallet. So, you could get ideas about how BitShares wallet works. 

* [The Open Public Testnet information](/core/testnets/public_testnet_details.md#public-testnet-details) (include Public Testnet Web UI wallet link) 
* [How to setup Public Testnet](/core/testnets/public_testnet.md#public-testnet-witnessfull-nodes-block-producing-witness-nodes) Witness(Full) Nodes (block producing witness nodes) 
* [How to setup Private Testnet](/core/testnets/private_testnet.md#private-testnet)

***



