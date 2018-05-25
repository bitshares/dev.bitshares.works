


Introduction
================
The BitShares Blockchain, as it exists today, was launched on 13th October 2015 with its community having found together already in 2013. It implements an industrial-grade decentralized platform built for high-performance smart contracts with focus on the financial technologies sector. 

Furthermore, BitShares represents the first decentralized autonomous cooperation that lets holders of its core native token BTS decide on its future direction and governance aspects. For sake of clarity and to avoid confusion with other smart contracting platforms, the BitShares Blockchain implements its contracts in form of *operations*. Even though the BitShares Blockchain comes with over 50 already implemented operations which deserve to be presented, this document focuses on the description of the BitShares Blockchain as a platform, its architecture as well as its governance system using the core native token BTS.


Architecture
===============
The BitShares Blockchain constitutes the following components which are described individually. 

Transactions
---------------
When users want to interact with any Blockchain, they build so called transactions. These present messages that contain instructions about what particular operation(s) a user wants to use. A common operation is the simple transfer operation that comes with a payload that provides the necessary information for this action, such as the sender, receiver, the amount to transfer as well as an optional encrypted memo. To allow multiple operations to take place subsequently, multiple operations can be bundled into a single transaction.
To identify against the system, transactions are cryptographically signed by the users. These signatures authenticate a user and provide authorization for the operations in the transaction. 

Blockchain
------------
The Blockchain serves as a journal of user-signed instructions that become a binding agreement as soon as they are included into a block. After inclusion into a block, the agreements are stored indefinitely by means of a hash-linked-list (the Blockchain). From this ordered sequence of transactions, the “current state” of the database (think: account balances) can be determined by processing all transactions consecutively starting at the very first block. As we will see later, the software will ensure that instructions that are stored in the Blockchain have been successfully authenticated and validated. For validating and processing of operations, a common set of rules define the consequences of particular actions, which are part of the of the blockchain protocol (see below).

Networking
------------
A blockchain merely defines a means of storage and can be used in a non-distributed, single-device fashion as well as in a distributed mesh network often referred to as Peer-2-Peer (P2P) network. There, multiple parties are connected with each other in a way that incoming transactions are forwarded to every node. A transaction ultimately reaches a so called block producer. A block producer verifies incoming transactions and bundles them into a single block that is added to the existing blockchain. At this point, a transaction is considered confirmed and executed. The effects of an executed operation on the current state of the database are defined in the blockchain protocol (below).

Consensus
-----------
Consensus is the process by which a community comes to a universally recognized, unambiguous agreement on a piece of information. In the context of blockchains consensus means agreement about the validity rules for transactions, and the order in which they have been observed by the blockchain. This ultimately results in an agreement about the state of a database that is build deterministically from the those validity rules and the sequence of transactions. 

The most commonly known consensus scheme is Proof-of-Work (PoW), that is used by many blockchains. Most dominant disadvantage is the heavy power consumption and the scalability in terms of transactions per second and confirmation times. The BitShares Blockchain makes use of a lesser known algorithm called Delegated Proof of Stake (DPoS) that was developed specifically to replace the wasteful ‘mining’ process, increase throughput and reduce reaction times of the blockchain. 

DPoS allows to generate a new block at fixed rate (block production/confirmation time) with minimal computational load. This means that the blockchain can process more transactions in significantly less time and at almost no cost when compared to PoW-based Blockchains . Block 1 production is performed by a set of so called witnesses that take turns. After every turn, the order of block producers is randomized in a deterministic manner such that all parties agree on the new order. 

Protocol
----------
The most essential part of blockchain technologies is here referred to as blockchain protocol. It defines the behavior of the entire system including consequences and side-effects when processing transactions. Users utilize particular features by crafting a transaction that contains a particular letter-of-interest (also referred to as operation). 
 
Since the Blockchain, as a storage, only stores incremental changes (e.g. transfers), the final balance of each account together with other informations needs to be tracked separately in the so called current state of the database.
 . 
It is important to note that the protocol is deterministic in the sense that the very same state of the database is generated when applying the same sequence of operations (as provided by the blockchain). This makes blockchain technologies tamper proof and auditable. 
 
In BitShares, over 50 operations are available (as of early 2018). Each of them hooks into the Blockchain protocol at least three times: 

- **Validation** : During validation, the operation’s payload is checked for consistency. E.g., in case of a transfer, we ensure that the amount to transfer is positive.

- **Evaluation** : In the evaluation step, the payload of the operation is validated against the current state of the blockchain database. In case of a transfer, we here ensure that the amount to be transferred is available in the account of the sender.

- **Application** : In step takes action in the sense that it modifies the current database. In the case of a transfer, we here reduce the account balance of the sender and increase the account balance of the receiver according to the amount of funds transferred. 


**Example** : Transfer operation
Consider a simple transfer operation that sends funds from one account to another. Here, the protocol defines the validation rules such that negative amounts are prevents. The evaluation ensures that the sender cannot transfer more than what is in his account balance. When applying a transfer from Alice to Bob, Alice is credited the transferred amount while Bob               receives the funds. Here, transfer refers to the operation type, while the sender, receiver, and amount refers to the operation payload. Obviously, different operation types come with different payloads.

Extensibility
--------------
The Software behind the BitShares Blockchain is well modularized and implements its operations independently of each other. This allows for adding new features once the corresponding code, which the implements validation, evaluation and application methods, reaches maturity. In a sense, operations on the BitShares Blockchain are smart-contracts and allows for extending the range of functions of the system. In contrast to other smart-contracting platforms, however, the BitShares Blockchain requires new features to be vetted by the core developers and approved by the BTS holders before they can be installed by means of a network-wide protocol upgrade. These protocol upgrades are well coordinated and already happened 27 times (Q1/2018) in the past.


Performance and Scalability
-------------------------------
The BitShares Blockchain publicly demonstrated sustaining over 3,000 (three thousand) transactions per second and over 22,000 operations per second on a distributed test network.              
This technology can easily scale to 10,000 (ten thousand) or more transactions per second with relatively straightforward improvements to server capacity and communication protocols. To achieve this industry-leading performance, BitShares has borrowed lessons learned from the LMAX Exchange , which is able to process 6 million transactions per second. Among these 2 lessons are the following key points: 

* Keep  everything  in  memory.
* Keep  the  core  business  logic  in  a   single  thread.
* Keep cryptographic operations (hashes and signatures)outofthecorebusiness logic.
* Divide  validation  into  state-dependent  and  state-independent  checks.
* Use  an  object  oriented  data  model. 

By following these simple rules, BitShares is theoretically able to process >10,000 (ten thousand) transactions per second without any significant effort devoted to optimization. 

|



