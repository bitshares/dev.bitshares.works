
.. _zero-to-sixty-guide:

***************************************************************
BitShares Development: Zero to Sixty in 30 Minutes or Less
***************************************************************

.. contents:: Table of Contents
   :local:
   
-----------------
   
Introduction to BitShares
============================

BitShares is a decentralized, blockchain-based financial services smart contracting platform. BitShares' decentralization is based on the Delegated Proof of Stake (DPoS) consensus model, meaning that blocks are produced by a group of 'Witness' nodes which are elected by stake-weighted shareholder voting. In addition to the Witnesses is the Committee, a group of blockchain accounts, likewise elected by stakeholder voting, which vote to specify tunable blockchain parameters, and vote to include or reject proposed new features and other modifications to the consensus protocol.

BitShares is a smart contracting platform specifically targeted at financial contracts. It should be noted that BitShares is not a turing-complete smart contracting platform, meaning that BitShares does not support arbitrary, user-defined smart contracts; rather, BitShares provides a well-defined set of officially maintained, built-in contracts. This is in contrast to turing complete smart contracting platforms like EOS or Ethereum, which provide few to no official contracts, but allow users to define and share contracts without any formally defined quality or correctness verification.

This document is intended to give new BitShares developers an introduction to the BitShares software. Readers are expected to be familiar with C++ software development in general, but not with BitShares specifically. This document will walk readers through the code repository structure and how to build the software; describe the individual libraries and executables and their purposes; provide a guide on starting a local testnet blockchain for development; and finally, examine how the contracts work and discuss how to create or modify BitShares smart contracts.

|  
  
The BitShares Repository
============================
The official BitShares repository can be found at https://github.com/bitshares/bitshares-core/

This repository uses git submodules, so be sure to fetch the submodules when cloning. This can be done by passing the ``--recursive`` flag when cloning::

  $ git clone https://github.com/bitshares/bitshares-core --recursive

The most significant subdirectories in the repository are ``libraries``, ``programs``, and ``tests``. The BitShares implementation is almost entirely defined within various libraries, which are located in the ``libraries`` subdirectory. The ``programs`` subdirectory contains small wrappers around these libraries, exposing their functionality as executable binaries. Finally, the ``tests`` subdirectory contains various tests to verify that essential blockchain features and functionality are working and to detect regressions should they occur during development.

We now examine each of these three subdirectories in greater detail.

The BitShares Libraries
--------------------------

BitShares is implemented in several ``libraries`` within the libraries subdirectory of the repository. A high level description of each of the libraries follows:

- ``app`` contains the ``application`` class, which implements the heart of a BitShares node
- ``chain`` contains the bulk of the blockchain implementation, including all BitShares-specific functionality
- ``db`` contains the database functionality, implementing the in-memory database as well as the persistence layer
- ``egenesis`` is a small library which embeds the genesis block data into the binary
- ``fc`` is a library implementing many utility functionalities, including serialization, RPC, concurrency, etc.
- ``net`` contains the peer-to-peer networking layer of BitShares
- ``plugins`` contains several plugin libraries which can be utilized within a BitShares node
- ``utilities`` contains code and data necessary to BitShares' implementation, but not critical to the core functionality
- ``wallet`` contains the reference command-line wallet implementation

Of these libraries, the bulk of development activity occurs within the ``chain`` library, and sometimes ``fc``. The other libraries remain reasonably stable, seeing comparatively small updates and modifications.

The BitShares Programs
--------------------------

BitShares contains several programs, but only two of these are relevant to modern BitShares development, namely ``witness_node`` and ``cli_wallet``; moreover, the code within these folders exists merely to expose library functionality in an executable, and is rarely updated. Consult the ``README.md`` in the ``programs`` directory for information on the other programs.

The ``witness_node`` program is the only maintained BitShares node executable. The name ``witness_node`` is something of a misnomer, as this executable is really just a full node, but it can provide witness (i.e., block producer) functionality by loading the ``witness`` plugin. If one wishes to sync with the BitShares blockchain network and maintain a database of the current chain state, this is the program to do it with.

The ``cli_wallet`` program implements a command-line wallet for BitShares. It requires a network connection to a running ``witness_node`` to provide chain state information to it. This program provides a basic UI for all BitShares functionality.

The BitShares Tests
--------------------------

BitShares uses the Boost testing framework for its tests. Most of the BitShares tests use the ``database_fixture``, defined in ``tests/common/database_fixture.hpp``, as the basis of the tests. This file also defines many macros and functions to reduce the boilerplate of test writing.

The bulk of the tests are written in the ``tests/tests`` folder, and are run by the ``chain_test`` binary. All tests of core functionality should be included in this directory and binary.

|

Building and Running BitShares
==================================

BitShares uses CMake as its build system. It is recommended to use a separate build directory, i.e. from within the BitShares repository, run::

	$ mkdir build # Make build directory
	$ cd build/   # Change to build directory
	$ cmake ..    # Run CMake to prepare build

If an error about ``GetGitRevisionDescription`` appears at this stage, most likely the submodules were not fetched (i.e. ``git clone`` was called without ``--recursive``). In this event, run ``git submodule update --init --recursive`` or simply delete the repository and clone again with the ``--recursive`` flag.

Another common problem at this stage is missing dependencies. Be sure that Boost and OpenSSL are available along with their development headers. If dependencies are installed to non-standard locations, it may be necessary to specify their install locations to CMake on the command line.

After any errors from CMake have been addressed, re-run CMake. Once CMake exits successfully, the build can be started simply by running ``make``.

When the build has completed, the most interesting binaries will be ``build/tests/chain_test`` which runs the tests, ``build/programs/witness_node/witness_node`` which is the full node binary, and ``build/programs/cli_wallet/cli_wallet`` which is the command line wallet.

When ``witness_node`` is run, it will create a folder for its persistent storage and configuration files. By default, this folder is located at ``$PWD/witness_node_data_dir``. This location can be overridden by using the ``-d /path/to/dir`` or ``--data-dir /path/to/dir`` command line options. If the directory does not exist, ``witness_node`` will create it with a default configuration file (called ``config.ini``) inside. This file contains comments describing its options, and should be straightforward to edit.

Running ``witness_node`` with the default configuration will cause it to connect to the main BitShares network and begin syncing the chain. For a list of command line options supported by the node, run ``witness_node --help``. Note that many of these options can also be specified persistently in the ``config.ini`` file.

Launching a Private Test Blockchain
-------------------------------------

Oftentimes it is useful to create a private test network when developing and testing new features. This can be done by configuring ``witness_node`` to use a custom Genesis block, and enabling block production using the witness accounts defined by that Genesis.

To start a node using a custom Genesis block, run ``witness_node --genesis-json /path/to/genesis-dev.json``. A suitable Genesis file is available within the repository at ``libraries/egenesis/genesis-dev.json`` (as of this writing, available in the ``develop`` branch, but not ``master``). It will also be necessary to set the following options in the ``config.ini`` file::

	# Open RPC socket for localhost (allows cli_wallet to connect)
	rpc-endpoint = 127.0.0.1:8090

	# This sets the private key used by all witnesses in genesis-dev.json
	private-key = ["BTS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]

	# This tells the node to produce blocks even if no recent blocks are available
	# This is disabled in production to prevent forking due to network failures, but it's necessary to start a new testnet
	enable-stale-production = true

	# Enable production with the genesis-dev.json witness accounts
	witness-id = "1.6.1"
	witness-id = "1.6.2"
	witness-id = "1.6.3"
	witness-id = "1.6.4"
	witness-id = "1.6.5"
	witness-id = "1.6.6"
	witness-id = "1.6.7"
	witness-id = "1.6.8"
	witness-id = "1.6.9"
	witness-id = "1.6.10"
	witness-id = "1.6.11"

Running witness_node as described should cause it to start a new chain and begin producing blocks. It should report its chain ID in the command line output::

	3166980ms th_a       main.cpp:160                  main                 ] Chain ID is ced68e68d7e41258f6a2e71643e41c690edae19dbed8c5f525a0f5c74d322fa9

Take note of this, as it will be necessary when running the command line wallet. If not provided, the wallet will refuse to connect to a ``witness_node`` that provides an unrecognized chain ID.::

	$ cli_wallet --chain-id ced68e68d7e41258f6a2e71643e41c690edae19dbed8c5f525a0f5c74d322fa9

Once the command line wallet is started, the following commands can be used to create a wallet file, take control of an account, and claim the core asset funds::

	>>> set_password hello
	>>> unlock hello
	>>> import_key init0 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
	>>> import_balance init0 ["*"] true

After this, all funds on the blockchain will be held by the ``init0`` account.

To get a complete list of commands supported by the command line wallet and their argument types, run help. For some commands, there is additional help available by running ``gethelp <command name>``, but in general, the best way to get information on the commands is to read `the source <https://github.com/bitshares/bitshares-core/blob/master/libraries/wallet/include/graphene/wallet/wallet.hpp#L321>`_ .


|

BitShares Smart Contracts
==================================

This section provides a high-level overview of the architecture of smart contracts in BitShares, how they work, and how they are created.

At its essence, BitShares smart contract is comprised of three main types of object: ``operation`` s, ``evaluator`` s, and ``object`` s. The BitShares protocol defines a set of actions a user can take within the blockchain ecosystem, called ``operation`` s. All interactions with the blockchain take place through ``operation`` s, and in a sense, they are the blockchain's API. Each ``operation`` has an ``evaluator``, which implements that operation's functionality within the BitShares software implementation. Thus an ``operation`` is like a function prototype, whereas an ``evaluator`` is the function definition. Finally, all data persistently stored by the blockchain is contained within database ``object`` s. Each ``object`` defines a group of fields, analogous to columns of a relational database table.

Operations
-----------------

A complete list of ``operation`` s defined by the BitShares protocol is stored `here <https://github.com/bitshares/bitshares-core/blob/57c40ecf472dd8c46ac082ed0e2f0292f147cf80/libraries/chain/include/graphene/chain/protocol/operations.hpp#L49>`_ . The individual ``operation`` s are defined within other headers in that same directory, i.e.  `transfer_operation <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/protocol/transfer.hpp#L44>`_ .

All ``operation`` s charge a fee to execute, and each must specify an account to pay the fee. This account's ID must be returned by the ``fee_payer()`` method on the ``operation``. Each ``operation`` must also provide a stateless consistency check which examines the ``operation``'s fields and throws an exception if anything is invalid. Finally, ``operation`` s must provide a ``calculate_fee()`` method which examines the ``operation`` and calculates the fee to execute it. This method may not reference blockchain state; however, each ``operation`` defines a ``fee_parameters_type`` struct containing settings for the fee calculation defined at runtime, and an instance of this struct is passed to the ``calculate_fee()`` method.

All ``operation`` s automatically require the authorization of their fee paying account, but an ``operation`` may additionally specify other accounts which must authorize their execution by defining the ``get_required_active_authorities()`` and/or ``get_required_owner_authorities()`` methods (i.e. for `account_update_operation <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/protocol/account.hpp#L161>`_ ). If a transaction contains an ``operation`` which requires a given account's authorization, signatures sufficient to satisfy that account's authority must be provided on the transaction.

Evaluators
------------------

Each ``operation`` has an ``evaluator`` which implements that ``operation``'s modifications to the blockchain database. Each ``evaluator`` most provide two methods: ``do_evaluate()`` and ``do_apply()``. The evaluate step examines the ``operation`` with read-only access to the database, and verifies that the ``operation`` can be applied successfully. The apply step then modifies the database. Each ``evaluator`` must also define a type alias, ``evaluator::operation_type``, which aliases the specific ``operation`` implemented by that evaluator.

For example, reference the ``transfer_operation``'s evaluator `here <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/transfer_evaluator.hpp>`_  and `here <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/transfer_evaluator.cpp>`_ .

Objects
------------------
The BitShares software implementation utilizes a custom, in-memory relational-style database to track the blockchain state as new blocks and transactions are applied, containing ``operation`` s which modify the database. This database is implemented in the ``libraries/db`` folder, and it provides persistence to disk as well as undo functionality allowing the rewinding of changes, such as when a partially-applied transaction fails to execute, or blocks are popped due to a chain reorganization (i.e. when switching forks).

The BitShares database tracks various ``object`` types, each of which defines the columns of a table. The rows of this table represent the individual object instances in the database. Along with each ``object`` type is an index type, which, in relational database terms, defines the primary and secondary keys, which can be used to look up object instances. The primary key is always an ``object_id`` type, a unique numerical ID for each object instance known to the blockchain. All ``objects`` inherit an ``id`` field from their base class which contains this ID. This field is set by the database automatically and does not need to be modified manually.

An example of a simple object is ``transaction_object``, defined `here <https://github.com/bitshares/bitshares-core/blob/master/libraries/chain/include/graphene/chain/transaction_object.hpp>`_ . The index is defined after the object. In this instance, the index defines the primary key (the object ID), and two secondary keys: the transaction ID (its hash), and the transaction's expiration. This means that one can look up a ``transaction_object`` given its object ID or with its transaction hash. Additionally, one can iterate through the ``transaction_object`` s sorted by expiration, or fetch transactions that expire within a given range.

Summary

BitShares smart contracts are defined as a set of ``operation`` s which are analogous to API calls provided by the contract. These ``operation`` s are implemented by ``evaluator`` s, which provide code to verify that the operation can execute successfully, and then to perform the requisite modifications to database ``object`` s. All ``object`` s specify an index, which defines keys which can be used to look up an object instance within the database.

A diff showing all necessary modifications to define a simple new ``operation`` and ``evaluator``, along with the ``evaluator``'s code to modify existing database ``object`` s, as well as test code to exercise this new ``operation``, is available for reference `here <https://github.com/nathanhourt/bitshares-2/pull/1>`_ .

|



Contributor: @nathanhourt

