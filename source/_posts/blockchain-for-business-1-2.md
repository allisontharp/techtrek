---
title: Blockchain for Business 1.2
url: 1867.html
id: 1867
categories:
  - Uncategorized
date: 2017-12-07 10:00:38
tags:
---

In the [previous blockchain post](http://www.techtrek.io/blockchain-for-business-1-1/), we discussed an overview of what blockchain is and dug deeper into DLTs.  This post will still be part of Chapter 1: Discovering Blockchain Technologies, but will take a deeper look into Bitcoin, consensus algorithms, and the challenges of implement DLTs.

### A Deeper Look into Bitcoin

As discussed previously, Bitcoin is perhaps the most well known blockchain application.  It is a decentralized payment network that has a distributed and publicly owned infrastructure (due to the P2P network).  With Bitcoin, a user can send and receive bitcoins anywhere directly with other peers without having to go through a third party such as a bank.  As of October 2017, bitcoin's market cap was nearly $100 billion. Bitcoin officially launched in January 2009 as a response to the 2008 financial crisis.  The big motivation at that time was to transfer value via the internet without needing to go through another entity, which people saw as a 'double spend' problem (in other words, the 3rd party takes their share of the money you transfer). Bitcoin's biggest "rival", Ethereum, was proposed in 2013 in a white paper by a 19 year old named Vitalik Buterin.  Unlike Bitcoin, which just tracks transactions, Ethereum also programs the transactions.  Its cryptocurrency is known as 'ether'.  In October 2017, it had a market cap of $28 billion. Ethereum is more of an open source platform.  It allows developers to build and deploy smart contracts and decentralized applications (known as Dapps).  This allows versions of Ethereum to be private and permissioned.  Some of the most well known dapps are: Auger, a decentralized prediction market and Digix, which is a cryptocurrency.

### Consensus Algorithms

#### Proof of Work (PoW)

Bitcoin's consensus algorithm is known as **Proof of Work** or PoW.  This consensus algorithm requires solving a challenging puzzle to create a new block in the Bitcoin blockchain.  This process of trying to solve the puzzle is known as 'mining' and all nodes in the network that mine are known as 'miners'.  The incentive to mine for Bitcoin is the economic payoff (you can sell your mined Bitcoins for "real" money). The nice thing about Proof of Work is that the proof is hard to create but it is easy to verify.  A good example of this is trying to guess the combination of a lock.  It is very hard to guess the right combination and you'll likely have to guess many different combinations.  However, once it is produced, it is easy to validate by just entering the combination in and seeing the lock open. There are downfalls to PoW.  First, it requires a huge amount of energy because the puzzles are computationally heavy algorithms.  This also causes the concentration of the mining power to be located in places where electricity is cheap.

#### Proof of Stake (PoS)

Another consensus algorithm is called the **Proof of Stake** algorithm.  With this algorithm, the nodes are known as validators and instead of mining the blockchain, they validate the transactions to earn a transaction fee.  Instead of creating new coins (as is the case in Bitcoin), all of the coins exist from the very beginning.  Another way to look at this is that the nodes are randomly selected to validate blocks.  The likelihood of the random selection will depend on how many coins the node holds (this is known as the amount of stake they hold).

#### Proof of Elapsed Time (PoET)

This consensus algorithm was created by Intel and emulates PoW.  However, instead of competing to solve really difficult cryptographic puzzeles, under PoET there is a hybrid of a random lottery and first-come-first-serve basis.  Each node (or validator) is given a random wait time.  The validator with the shortest wait time is elected the leader, and they get to create the next block in the chain.

#### Simplified Byzantine Fault Tolerance (SBFT)

This algorithm was created to try to provide improvements over Bitcoin's PoW algorithm.  With SBFT, a single validator bundles transactions and forms a new block.  This validator is a known party, which is unlike what happens in Bitcoin.  Consensus under SBFT occurs when the minimum number of other nodes in the network ratify the new block.

#### Proof of Authority (PoA)

This consensus algorithm is used in permissioned ledgers.  For PoA, there are designated nodes which are allowed to create new blocks and secure the ledger.  These nodes are known as 'authorities'.  In order to create the block, the ledgers require sign off by a majority of authorities.

### Challenges in the Adoption/Deployment of DLTs

The biggest challenge when adopting Distributed Ledger Technologies right now is that there is a lack of standards and knowledge.