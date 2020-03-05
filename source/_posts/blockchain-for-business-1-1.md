---
title: Blockchain for Business 1.1
url: 1854.html
id: 1854
categories:
  - Uncategorized
date: 2017-12-05 11:39:19
tags:
---

I'm currently enrolled in the edX course [Blockchain for Business - An Introduction to Hyperledger Technologies](https://www.edx.org/course/blockchain-business-introduction-linuxfoundationx-lfs171x) and thought it would be a good opportunity to resume the blog. I first heard about blockchain just a few weeks ago at a work conference.  I knew about bitcoin but hadn't considered that it was part of a larger idea.  I know very little about bitcoin and even less about blockchain in general, so I'm excited to see what the course has to offer.  I also haven't done any online courses like this, so I'm also looking forward to seeing how I like it.

### Chapter 1: Discovering Blockchain

#### Objectives:

*   Discuss blockchain and distributed ledger technologies
*   Explore permissioned and permissionless blockchains and their key characteristics
*   Discuss various components of distributed ledger technologies, including consensus algorithms and smart contracts
*   Provide a high-level explanation of what Hyperledger is

#### Current Knowledge

I currently don't know anything about blockchain, distributed ledger technologies, or Hyperledger so anything that this chapter will offer will be a big step towards meeting the chapter objectives.

### Distributed Ledger Technology (DLT)

A _distributed ledger_ is a data structure that is spread across multiple computers (which are usually spread across locations or regions).  Distributed ledger technologies have three basic components:

*   A data model to capture the current state of the ledger
*   A language of transactions to track the changes in the ledger state
*   A protocol that builds consensus among participants around which the transactions can be accepted

In other words, we can think of a distributed ledgers as databases which are shared among peers and do not rely on any central authority or intermediary.  Instead of having a central database, every participant has their own copy which stays in sync via the pre-established protocol.  Each participant verifies transactions and speaks a common language to ensure universal agreement on the state of the ledger. Distributed Ledger Technology has been proposed for things such as insurance claim processing, supply chain management, and the Internet of Things. To connect to a blockchain, a person needs to connect to the distributed ledger.  This is generally done through an application.  Using cryptocurrency (such as Bitcoin) as an example, the application would be a 'wallet'.  One person may transfer the cryptocurrency ownership to another person, which will move it from person A's wallet to person B's.  This transaction will be shown on a blockchain.  This transaction moves from peer-to-peer throughout the network, without any intermediaries (like a bank or payment company) to process the transaction.  Blockchains sync up among network peers via the protocol.  This protocol ensures that all machines on the network eventually have an exact copy of the blockchain. In other words, the DLT means that there is an exact copy of the database for each peer that is participating in transactions on the database.

### Blockchains

Hyperledger.org defines a blockchain as a peer-to-peer distributed ledger forged by consensus, combined with a system for "smart contracts" and other assistive technologies.  In this case, _consensus_ means that a system is ensuring that all parties agree that a certain state of the system is the true state and a _smart contract_ is a computer program that executes predefined actions when certain conditions within the system are met. Blockchain is a specific form of DLT which consists of a chronological chain of blocks (hence the name).  As discussed previously, a block is a set of transactions which are bundled together and added to the chain at the same time.  For Bitcoin, the miner nodes bundle unconfirmed and valid transactions into a block, which contains a given number of transactions.  Within the network, miners solve a cryptographic challenge to propose the next block.  This process is known as proof of work and requires a lot of computing power. Another key component to a Blockchain is timestamping.  Each block is timestamped and each new block refers to the previous one.  When combined with cryptographic hashes, the timestamped chain of blocks provides an immutable record of all transactions in the network from the current block to the initial (genesis) block. Generally, a block will consist of four components:

*   A reference to the previous block
*   The proof of work (also known as nonce)
*   A timestamp
*   The Merkle tree root for the transactions included in the block

### DLT vs Blockchain

The terms Distributed Ledger and Blockchain are becoming muddier as the technology becomes more mainstream.  However, if we are trying to distinguish the two, we can think of Distributed Ledgers or DLTs as a very specific way to talk about a decentralized database.  It is a way that every peer on the network can have a copy of the series of transactions in a very synchronized way.  The term Blockchain refers to the specific data structure.  I believe this is similar to the difference between a relational database system (Blockchain) and SQL (Distributed Ledger).

### The Merkle Tree

Also known as a binary hash tree, a Merkle tree is a data structure that stores hashes of the individual data in large datasets in a way that makes the verification of the dataset efficient.  Its purpose is to make sure that a large dataset has not been changed.  They are used to summarize the transactions in a block by creating a digital 'fingerprint' of the entire set of transactions in the block.  This process makes it really efficient to verify if a transaction is included in a block or not.

### Transactions

A blockchain transaction is simply just the record of an event.  It is secured cryptographically via a digital signature which is verified, ordered, and bundled together into blocks.  For Bitcoin, transactions record the transfer of bitcoins.  A smart contract, as discussed previously, may allow automatic execution of transactions upon meeting some predefined critia. Cryptography is the study of techniques used to allow secure communication between different parties and to ensure the authenticity and immutability of the data communicated between them.  Blockchain uses cryptography to prove that a transaction was created by the right person as well as to link transactions in a tamper-proof way.

### Blockchain vs Relational Databases

Blockchains are write-only.  This means there are no higher level permission sets (such as administrator permissions) that allow data to be edited or deleted.  Instead, users can only write to blockchains.  Each new block is appended to the chain by linking to the previous block's hash.  In addition, blockchains were designed to be decentralized (where every user has an exact copy of the blockchain). Relational databases allow data to be added, modified, and deleted.  Obviously, there are usually different permission levels so that not every user can edit or delete data, but the technology allows for data manipulation.  Finally, relational databases are centralized, meaning one entity controls all of the data.

### Types of Blockchains

In general, there are two types of blockchains: permissionless and permissioned.  A **permissionless** blockchain (also known as a public blockchain) allows anyone to join the network.  In a **permissioned** blockchain (also known as private blockchain), parties must pre verify that they can join the network.  An example of a public blockchain is Bitcoin and an example of a private blockchain would be the different Hyperledger blockchain frameworks. Most enterprises involve extensive pre verification before both parties agree to do business with eachother, so in this case, a private blockchain is appropriate.  When a network has trust and all parties can participate in transactions without needing to verify each user's identity, a public blockchain may be more suitable.

### Network Architecture

Many application, especially historically, use a central server.  This means there is one server (or group of servers) that each user must connect to in order to use the application.  This is much like how a relational database fundamentally works. In the late 1990's, peer to peer (P2P) systems started to become popular with the rise of the music sharing application Napster.  In a P2P network, computers are connected _directly _to one another via the internet, without the need of first connecting to a centralized server.  This allows all participants in the network to contribute to the computing power and storage required by the network.  These systems are generally considered more secure since there isn't a centralized source to attack. As with blockchain, there are permissionless and permissioned P2P networks.  Permissionless do not require a set number of peers to be online at a given time and they are generally slower.  Permissioned P2P networks have guaranteed uptime and have a high level of quality of service on the communication links. It seems like P2P networks are the grandfather of blockchain, or that blockchain is an evolution of P2P networks!

### **Immutability of Data**

The immutability of (or inability to change) the data is one of the most compelling reasons to switch to blockchain technology.  Some industries where this is extremely useful are accounting, identity management, and asset ownership because once a transaction is written to the blockchain, no one can change it. Up until this point, we've discussed that the data in a blockchain can't be changed.  This isn't 100% true.  It is possible to change data in a blockchain, but it is extremely difficult to change and extremely easy to detect the attempt. It is hard to change data in a blockchain because each block is linked to the previous block (via that block's hash).  The hash includes the Merkle root hash of all transactions in the previous block.  Therefore, if you were to change the data in one block, you would also change the Merkle root hash and the hash in the changed block.  Also, every block after the changed block needs to be updated to reflect this change.  The amount of energy required to recalculate each block is prohibitive.  If someone were to just change the data in a given block and not update everything after it, it would be easy to recalculate what is wrong.