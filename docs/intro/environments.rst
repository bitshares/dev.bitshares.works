.. role:: strike
    :class: strike
	
**************************
Development Environments
**************************

.. contents:: Table of Contents
   :local:
   
-------------------

BitShares Code and Files 
===============================

* Open Source program
* Language uses (mainly): BitShares-Core(C++), python
* `BitShares GitHub <https://github.com/bitshares>`_

   - **BitShares-Core (C++)** - BitShares Blockchain implementation and command-line interface.
   
    + `Programs <https://github.com/bitshares/bitshares-core/tree/master/programs#bitshares-programs>`_ : The bitshares programs are a collection of binaries to run the blockchain, interact with it or utilities.	 
	   
    + `Libraries <https://github.com/bitshares/bitshares-core/tree/master/libraries#bitshares-libraries>`_ : The libraries are the core of the project and defines everything where applications can build on top.
	 
   - **Bitshares-FC** - Fast-compiling C++ library 
   - **BitShares python** - Fully featured client-side library for the BitShares Blockchain - written entirely in python.
   - **BitShares-UI** - Fully featured Graphical User Interface / Reference Wallet for the BitShares Blockchain.
   - **BSIPs** - BitShares Improvement Proposals and Protocols. These technical documents describe the process of updating and improving the BitShares blockchain and technical ecosystem.
   - **BitSharesjs** - JavaScript tools for BitShares Encryption and Serialization.
   - **BitSharesjs-ws** - Javascript websocket interface for Bitshares 
   - **Beet** - Beet is a stand-alone key/identity-manager and signing app for BitShares, heavily influenced by Scatter
   - **Bitshares Mobile App** - The mobile app for bitshares blockchain
   - (more...)

----------
   
What's your interests?
========================

What would you like to know more about BitShares Blockchain? BitShares Core is the BitShares blockchain implementation and command-line interface. If you are interested to know about BitShares blockchain background, history, and features, please visit `how.bitshares.works <http://how.bitshares.works/en/latest/#>`_ and `bitshares.org <https://bitshares.org/>`_ to read more information. 

If yo do not have a BitShares account yet, you can use BitShares UI wallet (`wallet.bitshares.org <https://wallet.bitshares.org>`_ ) or download a `Light Client Wallet <https://bitshares.org/download>`_  to create your BitShares account. Here is the latest `BitShares-UI – Release <https://github.com/bitshares/bitshares-ui/releases>`_ information. 

If your interests are trying and learning BitShares features, you might want to use BitShares TestNet to experience them. If you are a developer who is interested to contribute to the BitShares Core team, you could download a developer branch and learn more about BitShares-Core current Project might be a good start. Select an appropriate branch when you install BitShares-Core.

After you know which branch to download, your next step is the installation of BitShares-Core. Check the :ref:`BitShares Installation Guide <installation-guide>` and select your Operation System to follow the installation steps. 

.. image:: ../../_static/imgs/your-interests.png
        :alt: BitShares
        :width: 750px
        :align: center
  
BitSharea has great communities to support others and have discussions. Check out the :ref:`BitShares communities <bitshares-communities>` and join to meet other BTS holders!     
  
|
  
----------------   
   
   
   
BitShares Core: Projects Guide 
==================================

If you are interested to know or involve BitShares-Core developments, you will find the contribution guide, current project, issues and plans in this section. 

Project Guide and Milestones 
------------------------------

- `Contribution Guide [DRAFT] <https://github.com/bitshares/bitshares-core/wiki/Contribution-Guide>`_
- `Project <https://github.com/bitshares/bitshares-core/projects/6>`_
- `BitShares-Core: current issues and requests <https://github.com/bitshares/bitshares-core/issues>`_ 
- `Milestones and Plans <https://github.com/bitshares/bitshares-core/milestones>`_ 
- `BitShares-Core Releases <https://github.com/bitshares/bitshares-core/releases>`_ 


BitShares-Core (Team) 
^^^^^^^^^^^^^^^^^^^^^^^

The BitShares-Core team is a team of developers who manage the BitShares-Core repository code and handle the issues that are submitted by other developers. The team creates project plans for next release(s) and delivers the result to the Bitshares community. 

* Roles

  - improving
  - maintaining
  - upgrading protocol if needed
  - making Project plans for the future release
  - creating/ announcing  Release 
  - supporting the BitShares community/ answering questions
  
	
------------------	
	
BitShares Core: GitFlow
=========================

Purpose
-------------

* The purpose of this document is to describe and define how changes flow into our code and through the various stages of development until it finally goes into production.
* The general idea is based on `git-flow <https://datasift.github.io/gitflow/IntroducingGitFlow.html>`_
* For our purposes, the general concept behind gitflow has been extended to allow for these additional needs:

1. We have two different types of releases, mainnet and testnet, with a master-like branch for each one.
2. We have to distinguish Consensus Impacting Changes (aka hardforks) from Non-Consensus Impacting Changes.


Non-Consensus: Development / Release / Bugfix Workflows
-----------------------------------------------------------

.. image:: ../../_static/structures/bts-non-concensus.png
        :alt: BitShares
        :width: 750px
        :align: center

Consensus: Development / Release / Bugfix Workflows
------------------------------------------------------

.. image:: ../../_static/structures/bts-concensus.png
        :alt: BitShares 
        :width: 750px
        :align: center


Goals To Achieve
---------------------

1. Maintain two independent release versions, testnet and mainnet.
2. Decouple development from releases, i. e. maintain the ability to create emergency bugfixes for current release without bringing incomplete new features into production.
3. Separate consensus-related changes from non-consensus-related changes.
4. Keep development branches compatible with mainnet.

Basic Rules
---------------

1. Development always happens in private feature-branches. The only exception is a change that must be distinguished in the destination branch (typical example: hardfork date in testnet).
2. Features are merged after they are reasonably complete, i. e. they come with unit tests that provide reasonable coverage and do not report any errors.
  - **Completed** features that are not consensus-related are merged into **develop**.
  - **Completed** features that are consensus-related are merged into the **hardfork** branch, with a hardfork date in the far future.
  - All merges into **develop** or **hardfork** are performed via github PR's and require review and approval from core source (if the PR is created by a core dev at least one other core dev must review and approve).
  - To maintain a clean history and make reviewing and merging easier, feature branches must be rebased onto current **develop** (or **hardfork**) before creating a PR.
  - Merges are always done as real merges, not as fast-forwards, and not squashed. 
3. Core devs coordinate regular merges from **develop** into **hardfork**.
4. Both **develop** and **hardfork** should always remain compatible with mainnet, i. e. a full replay must be possible.

|

--------------

How To Create a Release
---------------------------

For a release,

0. Bump stuff
 1) Check whether need to bump ``DB_VERSION`` to force a replay after upgraded: if there is a data schema change, or logic change that affects historical data, the answer is yes.  
 2) FC version usually has been bumped already during development, but it doesn't harm if check again.  
 3) Bump docs sub-module which links to wiki.
1. A **release** branch is created based on **develop** or **hardfork**.
2. Update Doxyfile with the last version tag in **release** branch.
3. The **release** branch is merged into **testnet**.
4. For a hardfork release, the hardfork date is adapted directly on the testnet branch.
5. The **testnet** branch is tagged as test-version.
6. Bugfixes for the release are created on the **release** branch and merged into **testnet**. Additional test-versions are tagged as needed.
7. After sufficient testing, the release must be approved. In the case of a hardfork release, witness approval is required.
8. After approval, the mainnet hardfork date is decided and set in the **release** branch.
9. The **release** branch is merged into **master**, and a version tag is created on **master**. Create tag by git command line and then point the release to the created tag in github. This is needed for Docker to catch the new code.
10. The **release** branch is merged back into **develop** and **hardfork**.
11. The **release** branch is merged into **testnet**. This will produce a merge conflict for the hardfork dates, which must be resolved without changing the testnet hardfork date.
12. Update online code documentation by using updated Doxyfile as config file in the master branch. Send pull request to https://github.com/bitshares/doxygen.bitshares.org with new content in html format. Send pull to https://github.com/bitshares/dev.bitshares.works with new content in xml format.
13. Update date of last core download page of bitshares.org site.
14. Create binaries for linux, macos and windows. Once the tag name is known create binaries for this 3 OS. Attach them to release notes.
**Example**: https://github.com/bitshares/bitshares-core/releases/tag/3.0.0 Binaries names for this release:
  
  - Linux: BitShares-core-3.0.0-Linux-cli-tools.tar.gz
  - Windows: BitShares-Core-3.0.0-Windows-x64-cli-tools.zip
  - macOS: BitShares-Core-3.0.0-macOS-cli-tools.tar.gz
	
**Note:** Solving conflicts by github(web) will merge branches in unintended directions. Avoid solving this way, merge and resolve conflicts manually through the git command line. Conflicts generally occur when merging release to testnet.

  - How to: `Snapshot Comparison in Bitshares <https://github.com/oxarbitrage/documentation/blob/master/snapshot.md>`_ 
  

**Note 2:** Follow command line github suggestion to resolve conflicts but at the end of the process you will not have permission to merge directly to ``testnet``, never push the fix to ``release``. Create a new branch and push there, then create a new pull request between ``testnet`` and ``new_branch``, merge ``new_branch`` to ``testnet`` and ``release`` will be automatically added to the merge.

**Note 3:** When creating tag for testnet do it from the command line with ``git tag``. Github don't have the option to create a tag without a release.

**Note 4:** :strike:`the tag commit can be changed`. Don't change tags on github. This is a source of confusion, and of irreproducible bug reports. Make new one is better (ex: test-2.0.180321b or wait 1 day).

**Note 5:** Do not mark releases as **pre release** unless there is a real new version coming immediately after. Never upgrade **pre release** to **release** as new emails to subscribers will not be sent when doing so.

|

--------------

How To Create an Emergency Fix
-------------------------------------

An emergency fix may become necessary when a serious problem in mainnet is discovered. The goal here is to fix the problem as soon as possible, while keeping the risk for creating additional problems as low as possible.

First of all, the problem must be analyzed and debugged. This happens, naturally, directly on the release version.

Presumably the developer who creates the fix will work on his private master branch. That is OK. But for publishing the fix, the following steps should be taken:

Emergency Fix Workflows
-----------------------------

.. image:: ../../_static/structures/bts-emergency-fix.png
        :alt: BitShares
        :width: 750px
        :align: center
		

1. The fix is applied to the version of the **release** branch that was merged into ``master`` when creating the broken release version.
2. The ``release`` branch is merged into ``master``, and a version tag is created on ``master``.
3. Witnesses update to the new version, and production continues.
4. A unit test is created on ``develop`` that reproduces the problem.
5. The ``release`` branch is merged into ``develop``, and it is verified that the fix resolves the problem, by running the unit test.
6. The ``release`` branch is merged into ``hardfork`` and ``testnet``.

|

-----------------------

How To Update the README.md
-----------------------------

In general we want changes in the README to be visible fast in ``master``.

1. Update the README.md of the ``release`` candidate.
2. Merge ``release`` to ``master``.
3. Merge back ``release`` to ``develop``.
4. Merge develop into ``hardfork`` and ``testnet`` if changes need to be visible in all the branches or wait until this merges occur naturally.

	

|

|

