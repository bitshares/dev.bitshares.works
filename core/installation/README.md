# Getting Started

#### Table of Contents:
- [Installation and Build](README.md#installation-and-build)
- [Next Step Guide](README.md#next-step-guide)
  - *If you want to...*
  
  
***

> See [System Requirements](../nodes_full_witness/full_nodes.md#system-requirements) if you are interested to run a node (updated: 2018-07-02).

BitShares Core is the BitShares blockchain implementation and command-line interface. The web wallet is [BitShares UI](https://github.com/bitshares/bitshares-ui).

Visit [BitShares.org](https://bitshares.org/) to learn about BitShares and join the community at [BitSharesTalk.org](https://bitsharestalk.org/).


> NOTE: The official BitShares git repository location, default branch, and submodule remotes were recently changed. Existing repositories can be updated with the following steps:

    git remote set-url origin https://github.com/bitshares/bitshares-core.git
    git checkout master
    git remote set-head origin --auto
    git pull
    git submodule sync --recursive
    git submodule update --init --recursive


***

# Installation and Build
Select an operation system and install and build.

- [Ubuntu Linux](../installation/build_ubuntu.md#building-on-ubuntu)
- [OS X](../installation/build_osx.md#building-on-os-x)
- [Windows](../installation/build_windows.md#building-on-windows)
- [Windows - CLI Tools](../installation/windows_cli_tool.md#cli-wallet-on-windows-x64)

#### Build and Run BitShares-Core in WSL (Installation Option)
- Use **Windows Subsystem for Linux**
  - [Instruction how to prepare Ubuntu to Windows 10 OS](../installation/wsl.md#windows-subsystem-for-linux-wsl) 

#### Known issues

- [Boost versions](../installation/boost_versions.md#boost-version)

***

# Next Step Guide

*If you want to...*

#### Create a BitShares GUI wallet and user account. 
- Check [BitShares-UI Release](https://github.com/bitshares/bitshares-ui/releases)
- Check [User Guide](https://github.com/bitshares/how.bitshares.works/tree/master/bbf/user_guide#user-guide)
- Refer [Create an Account](https://github.com/bitshares/how.bitshares.works/blob/master/bbf/user_guide/create_account.md#create-a-bitshares-wallet) steps

#### Create a CLI wallet
- Understand [BitShares Account](../accounts/README.md#bitshares-accounts)
- Check [CLI Client and Wallet - Architecture](../wallet/README.md#table-of-contents)
- Check [Connecting a CLI wallet](../wallet/cli_wallet.md#connecting-a-cli-wallet) steps

#### Run a node
- Understand [BitShares Account](../accounts/README.md#bitshares-accounts)
- Understand about a [BitShares Node](../nodes_full_witness/README.md#bitshares-nodes-and-p2p-network) 
- KNow the [Node configuration and System Requirements](../nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)
- Learn [Running a BitShares API Node](../nodes_full_witness/running-api-node.md#running-a-bitshares-api-node) steps 
- Try [BitShares Public TestNet](../testnets/README.md#testnets)

#### Run a witness node
- Understand [BitShares Account](../accounts/README.md#bitshares-accounts)
- Understand about a [BitShares Node](../nodes_full_witness/README.md#bitshares-nodes-and-p2p-network) 
- Know the [Node configuration and System Requirements](../nodes_full_witness/full_nodes.md#full-nodes-witness-nodes)
- Learn [Running a BitShares API Node](../nodes_full_witness/running-api-node.md#running-a-bitshares-api-node) steps 
- Learn [how to become an Active Witness](../nodes_full_witness/active_witness.md#become-an-active-witness)
- Try [BitShares Public TestNet](../testnets/README.md#testnets)

#### Use BitShares API 
- Check [BitShares-Core API References](../api/API_references.md#bitshares-core-api-references---doxygen-documentation-quick-links) page.
- Learn [API Restrictions](../api/api_restrictions.md#api-access-and-restrictions)
- Understand [Objects and IDs and the format](../api/object_ids.md#objects-and-ids)
- Understand BitShares' [Remote Procedure Calls (RPC)](../api/rpc.md#remote-procedure-calls) 
- Understand BitShares' [Websocket Calls & Notifications](../api/websocket_calls_notifications.md#websocket-calls-notifications)
- Check out [Often Used API Calls](../api/often_used_call.md#often-used-api-calls)


#### Learn BitShares system components
- [block](../components/README.md#components-structures-and-descriptions)
- [Operations and Detailed Descriptions](../components/operations.md#bitshares-core---graphenechain)
- [Object Names and Detailed Descriptions](../components/objects.md#object-names-and-detailed-descriptions)


#### know about BSIP
- Read [the Guide](../knowledge_base/bsip_funding.md#businesses-developers-and-business-developers)
- Check the [BSIP Repository](https://github.com/bitshares/bsips)




