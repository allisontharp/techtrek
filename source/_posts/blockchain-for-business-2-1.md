---
title: Blockchain for Business 2.1
url: 1875.html
id: 1875
categories:
  - Uncategorized
date: 2017-12-18 10:00:40
tags:
---

### Chapter 2: Introduction to Hyperledger

In this chapter, we will dive deeper into Hyperledger (the open source blockchain project started by the Linux Foundation).  The chapter will give more information on the project, its frameworks, and modules.

#### Objectives

*   Explain the differences between Hyperledger and permissionless blockchain technologies
*   Discuss how Hyperledger leverages open standards and open governance to support business solutions
*   Discuss Hyperledger frameworks (Iroha, Sawtooth, Fabric, Indy, and Burrow) and modules (Cello, Explorer, and Composer)

#### Current Knowledge

I have a feel for the difference between permissioned and permissionless blockchain technologies, so I think the first objective won't be completely new to me.  However, the second two objectives will be mostly new.

### What is Hyperledger?

Hyperledger is an open source project started by The Linux Foundation which hopes to advance blockchain technologies.  One of its main goals is to reduce the cost and complexity of doing business with blockchain.  Collaborators to the project include companies technology, finance, banking, supply chain management, manufacturing, and IoT leaders and companies. The blockchains in Hyperledger are permissioned and are specifically designed to be enterprise solutions.  Since it is a permissioned system, only specific people can join the network.  This makes it more secure, since an administrator can limit the access to the blockchain.  Additionally, instead of all transactions being available to everyone on the network, they are only displayed to the parties involved. The Hyperledger Project began when companies began to see Bitcoin rising and started wondering how they could use this new blockchain technology.  These companies reached out to The Linux Foundation to do a project together. About 30 companies came together to the Linux Foundation in December 2015 and released the first version of the software in just two months.

### Hyperledger Goals

Hyperledger provides a neutral space for software collaboration between companies which have traditionally been competitors.  It also provides a neutral space for cross-industry collaboration.  Blockchain technology is perhaps the easiest fit for the financial services industry.  However, in order to best use this new technology, all players in the field need to work together to create standards.  The stated goals for Hyperledger are to:

*   Create enterprise grade, open source, distributed ledger frameworks and code bases to support business transactions
*   Provide neutral, open, and community driven infrastructures supported by technical and business governance
*   Build technical communities to develop blockchain and use cases
*   Educate the public about the market opportunity for blockchain technology
*   Promote the community of communities taking a toolkit approach with many platforms and frameworks

As discussed in Chapter 1, one of the main struggles with distributed ledger technologies right now is the lack of standards.  One of Hyperledger's main goals is to come up with standards that make sense for all parties involved.  One way Hyperledger is doing this is to not promote its own technology, but to provide a neutral space for a variety of standards to exist simultaneously. Hyperledger is trying to build a developer ecosystem around its technologies.  They also work closely with their collaborators and users to understand where they are using the technology and what the value is that they are getting from it.

### Software Governance

Open source software means the code is freely available and is distributed and modified by a community.  Anyone can see, use, change, copy, or even contribute to the code.  With open governance, the technical decisions for the open source project are made by community elected developers.  This group of people will decide things like which features to add, how, and when to add them.  In other words, with open governance,  community elected group of people oversee the general direction of the project. At the core of Hyperledger, there is a set of maintainers.  These people are generally people that were with the project from early on and they are trusted by the community.  Everything this group does (such as commit to the source code repository or approving a patch) is public, which creates an accountability for their actions.  If a community member disagrees with something that a maintainer does, they can speak up and question the code or decision.  The maintainers can come to a decision to reverse the commit or decision.  In addition to these responsibilities, the maintainers also determine the roadmap of the project.

### Hyperledger's Frameworks

Hyperledger currently has five frameworks.  Each framework includes four main things:

*   An append-only distributed ledger
*   A consensus algorithm for agreeing to changes in the ledger
*   Privacy of transactions through permissioned access
*   Smart contracts to process transaction requests

**Hyperledger Iroha** The Hyperledger Iroha framework has been contributed to by Soramitsu, Hitachi, NTT Data, and Colu.  It is designed to be easy to incorporate into infrastructure projects and focuses on mobile app development for Android and iOS, which makes it unique from the other Hyperledger frameworks.  It seeks to complement Hyperledger Fabric and Sawtooth while also providing a development environment for C++ developers to contribute to Hyperledger. **Hyperledger Sawtooth** This framework has been contributed to by Intel and utilizes a modular platform for building, deploying, and running distributed ledgers.  These solutions can utilize various consensus algorithms based on the size of the network but by default, it uses the Proof of Elapsed Time (PoET). Sawtooth is designed to be flexible enough that you can grow the size of the network.  It is also flexible in that you can change the consensus algorithm on the fly. **Hyperledger Fabric** This framework provides a modular architecture that allows things like consensus and membership services to be 'plug and play'.  This is an implementation of a permissioned blockchain, which again, means that only certain parties can participate in the network. **Hyperledger Indy** This project was built specifically for doing distributed identity.  For example, within this framework, you have an identity that belongs only to you.  This identity correlates to pieces of data between you and the identities that you interact with, without also including private information that you don't want shared across the different interactions.  The main goal is to develop a set of decentralized identity specs and artificats that are independent of any particular ledger and will work across any distributed ledger technology that supports it. Rather than having a central entity or business store huge amounts of personal data on individuals, this project allows businesses to store pointers to identities.  Once the company verifies the other party's identity, it throws the pointer away.  This is important to companies because holding on to identifying information can be a huge liability to organizations. **Hyperledger Burrow** This project is formally known as **eris-db** and was released in December 2014.  It is a gateway that provides interfaces for systems integration and user interfaces.

### Hyperledger Modules

The Hyperledger modules are softwares used for things like deploying and maintaining blockchains and examining data on ledgers. **Hyperledger Cello** This module provides a toolkit which allows businesses to deploy Blockchain as a service. **Hyperledger Explorer** This module is a tool for visualizing blockchain operations and was the first ever explorer for permissioned ledgers. **Hyperledger Composer** This module is a suite of tools for building blockchain networks for businesses.  The tools in this module allow the user to model the network, create REST APIs for interacting with the network, and generate a base Angular application.  It is built in Javascript and is made to be easy to use so that developers can quickly implement it.