---
title: Blockchain for Business 3.1
url: 1884.html
id: 1884
categories:
  - Uncategorized
date: 2017-12-20 10:00:18
tags:
---

The previous two chapters of this course have been overviews of the technology and different development projects for the technology.  This chapter will focus on helping teams determine if blockchain is the right solution for their company or problem.

### Chapter 3: The Promise of Business Blockchain Technologies

**Objectives**

*   Examine several use cases where blockchain technology is actively used to solve real world business problems
*   Discover the factors to look at when evaluating if blockchain technology is right for a particular project
*   Decide when to use and when not to use blockchain technology

### Blockchain Supply Chain Use Case

Supply chain management is the oversight of the funds, materials, components, and finished products as they move from suppliers, manufacturers, wholesalers, retailers, and ultimately to the consumers.  All of this movement usually occurs both within one company and also among several companies.  A good supply chain management system will keep product quality consistent and prevent over and understocking of inventory. Companies want, and need, to ensure that their products are available when needed but overstocking their inventory is costly, particularly for perishable foods. One of the weakest parts of supply chain management is when there are multiple resource planning systems throughout the supply chain (usually due to multiple companies being involved in the supply chain).  The data doesn't necessarily flow well through each system, which makes the transfer of ownership between the parties a weakpoint. Blockchain helps supply chain in a number of ways.  First, there is an audit trail of every transaction that occurs within the system.  Because all parties can see all past transactions within the network, any participant is able to detect is a product is not as advertised.  For example, if you own a seafood restaurant, you may want to know when your fish was caught, who caught it, the temperature at which the fish was transported, and all of the transfers of ownership (from the fisherman and ultimately to you).  As the restaurant owner, you can audit all transactions in this network to make sure the fish is what you expected.  Instead of just examining the raw material, components, or finished product at different points in the supply chain, you have immutable data to back up what actually happened throughout the chain.  This creates a trust and accountability between all parties in the chain.  One of the cooler things that I think could potentially come out of blockchain technologies becoming more mainstream is this idea that all products, even the fish you eat, may have sensors tagged on them as soon as they are first introduced into their supply chain.  The sensors could track things like shock, temperature, and humidity so that you, as a consumer, can feel confident that the food you are eating or the product you are buying was handled properly throughout its life prior to you purchasing or using it. Although a record of the transaction is public, things such as the quantity of the goods or the identities of the party can be done pseudo-anonymously.

### Provenance

Provenance is the chronology of the ownership of an object.  It requires a lot of work, so in the past it has only been available for really expensive items such as famous works of art.  However, with blockchain, the idea of provenance is becoming available for a wider range of goods. This idea is exciting to consumers who want to make sure that fair trade and labor standards have been upheld in the products they purchase.  It will also be used by consumers who want to make sure the products they purchase were not tested on animals or by people who want to make sure no harmful chemicals were used during the manufacturing process.  Consumers who feel very strongly about issues like these are willing to pay a premium to make sure they aren't funding things they disagree with.  However, as with anything, this creates a counterfeit market that takes advantage of these consumers. To make sure a product is truly authentic, you need a record of all of the transactions for the entire life of the item prior to your purchase.  For things like works of art, there may be a trusted third party that keeps this record.  However, since there is a third party, transactions for this item will cost more as the third party takes a cut of the transaction fee.  Some third parties that do this certifying now are the USDA for produce, Fair Trade USA for human worker conditions, and the GLA for jewlery. Blockchains could replace these third parties by tracking the products throughout their lifetime.  Alternatively, these third parties could use blockchains to record their audits and inspections.  Using blockchain would reduce the overhead needed to certify products as well as create an immutable ledger, which would build trust even more. For example, a manufacturer could prove that its sources for raw materials also abide the standards that it needs to abide by if those sources are listed on blockchains as having passed all requirements.  The source's original certification and renewals could then be viewed by any interested party throughout the supply chain. As a concerned consumer,  you could verify a product's authenticity by seeing the fully chain of custody for the item.  Hyperledger frameworks would allow a consumer to view important data about the goods without necessarily seeing details like who exactly conducted each transfer.

### Finance Use Case

Finance is maybe the most obvious industry to adapt blockchain in.  It isn't surprising that the biggest blockchain application right now (Bitcoin) is in the finance sector.  In fact, the very first blockchain use case was for payments! Bitcoin has proven itself to be pretty slow for payments, averaging about 7 transactions per second.  Compare this to Visa, which averages about 2,000 transactions per second and has a peak capacity of 50,000 transactions per second, and it becomes clear that there is a lot of room for improvement.  Because of this, developers are working hard to increase the throughput of Bitcoin and its competitors. Benefits of blockchain as a use for financial transactions was talked about previously.  As an overview, these benefits include a decrease in cost for payments because you can eliminate the intermediary (such as a bank) and there is a record of all past payments (which is useful for auditors and regulators).

### Healthcare Use Case

Medical insurance enrollment information is already on blockchain for verification purposes and the industry plans to incorporate other aspects of claims processing on blockchain.  Health insurance providers must audit their care providers by verifying whether a practitioner actually delivered the care that he or she was obliged to deliver to the patient.  They also must audit the financial aspects that incurred as part of the treatment, ensure it was paid, and that the charges are accurate.  There is a **lot** that goes into this, especially when you consider that coding for the care could be incorrect, the charges could be incorrect, the payments could be incorrect, etc. One area that could be really beneficial is having more patient care information on the blockchain.  However, since most or all information on the chain is visible to all parties involved, this creates a potential PHI (Protected Health Information) issue.  People at places like Hyperledger are currently trying to figure out the best way to keep confidential information on a blockchain confidential.

### When to (Not) Use Blockchain Technologies

Blockchain is generally best suited for applications where one or more of these are true:

*   There is a need for a shared and common database
*   The parties have conflicting incentives or there is not trust among all participants
*   There are multiples writers to a database
*   There is currently a trusted third party that facilitates that interactions between other parties.  This could be escrow services, data feed providers, licensing authorities, or a notary public
*   Cryptography is currently (or should be) used
*   Data is being entered into many different databases along the lifecycle of the process and it is important that the data is consistent
*   There are rules that all participants in the system must abide by
*   Decision making among the parties is transparent
*   There is a need for an immutable history
*   Transactions don't exceed 10,000 transactions per second

Blockchain isn't well suited if the following is true:

*   The process involves confidential data
*   The process stores a lot of static data or the data is large
*   Rules of transactions change frequently (though as discussed in part 2.1, this isn't always a deal breaker)
*   The use of external services to gather or store data