
********************
Getting Started
********************      
	  
Development Environment

BitShares offers you to install BitShares-Core into different platforms; **Ubuntu (x64), OSx, and Windows**.  There are  dependencies to check when you download OpenSSL and  Boost. Please make sure which versions you downloaded. 

Also, if you are a Windows user, you have other two choices to install BitShares Core to your Windows (x64) Operation System.  One is **CLI-Wallet tools for Windows (x64)**, another is **Windows SubSystem for Linux (WSL)**.  

The CLI-wallet tools for Windows (x64) allows you to try several methods to get the data from BitShares blockchain without the BitShares Core installation. After you download the cli-wallet tools, you will find a cli_wallet executable to try Wallet API. 

Another option, Windows SubSystem for Linux (WSL).  This is for a developer who uses a Windows 10 (x64) Operation System and wants to build BitShares Core on Ubuntu. 

> Please check [System Requirements](../nodes_full_witness/full_nodes.md#system-requirements) to run a node (updated: 2018-07-02).

-------------

BitShares Core is the BitShares blockchain implementation and command-line interface. The web wallet is [BitShares UI](https://github.com/bitshares/bitshares-ui).

Visit [BitShares.org](https://bitshares.org/) to learn about BitShares and join the community at [BitSharesTalk.org](https://bitsharestalk.org/).


> NOTE: The official BitShares git repository location, default branch, and submodule remotes were recently changed. Existing repositories can be updated with the following steps:

    git remote set-url origin https://github.com/bitshares/bitshares-core.git
    git checkout master
    git remote set-head origin --auto
    git pull
    git submodule sync --recursive
    git submodule update --init --recursive

|


Installation
=================

.. toctree::
    :maxdepth: 2
    :numbered:
    :caption: BTS Holders
	
    build_ubuntu
    build_osx
    build_windows
    windows_cli_tool
	
	
	
	
- [Ubuntu Linux](../installation/build_ubuntu.md#building-on-ubuntu)
- [OS X](../installation/build_osx.md#building-on-os-x)
- [Windows](../installation/build_windows.md#building-on-windows)
- [Windows - CLI Tools](../installation/windows_cli_tool.md#cli-wallet-on-windows-x64)

Build and Run BitShares-Core in WSL (Installation Option)
--------------------------------------------------------------
- Use **Windows Subsystem for Linux**
  - [Instruction how to prepare Ubuntu to Windows 10 OS](../installation/wsl.md#windows-subsystem-for-linux-wsl) 

--------------

Known issues
---------------

- [Boost versions](../installation/boost_versions.md#boost-version)


|

|
