---
title: What Is NoSQL?
tags:
  - NoSQL
url: 877.html
id: 877
categories:
  - Methodology
date: 2016-03-25 08:00:56
---

Up until now, all of my database posts have been some form of SQL ([MySQL](/building-your-first-database/), [SQLite](/an-introduction-to-sqlite/), [SQL Server](/installing-sql-server-express/)).  However, SQL isn't the only kind of database management system and there are some benefits to using something besides SQL.  In general, these databases are known as **NoSQL** databases.

### What is NoSQL?

NoSQL are sometimes also called **cloud databases**, **non-relational databases**, or **Big Data databases**.  It was originally developed because we started making applications that created, stored, and analyzed huge amounts of new and rapidly changing datatypes. NoSQL databases are commonly the first alternative to relational databases (SQL).  Relational databases weren't designed to handle the scale and agility challenges that we are facing with our modern applications.  NoSQL was designed to take advantage of modern storage and processing power.

### Types of NoSQL Databases

There are four main types of NoSQL databases:

*   **Graph database** \- Used to store information about networks of data (think social connections) when the networks or relationships are well represented as a graph.  It is based on [graph theory](https://en.wikipedia.org/wiki/Graph_theory) .
    *   Examples: Neo4J, Giraph, Titan
*   **Key-Value store **\- One of the simplest NoSQL options.  They are designed to store data with a key-value store instead of a schema.  With a key-value store, all the data has an attribute name (key) and value
    *   Examples: Cassandra, DyanmoDB, Azure Table Storage (ATS), Riak, BerkeleyDB, Redis
*   **Column store **(aka wide-column store) - Optimized for queries over large datasets and stores columns of data together, instead of rows.  These offer very high performance and high scalability when compared to a standard database
    *   Examples: Cassandra, HBase, BigTable, HyperTable
*   **Document database** \- A more complex version of key-value stores.  Here, 'documents' are complex data structures that each have their own unique key.  The documents can have many key-value or key-array pairs.  Nested documents are also possible.
    *   Examples: MongoDB, CouchDB

### Advantages Over RDBMS

NoSQL databases are more scalable and can provide superior performance and address issues that RDBMSs are not designed for:

*   Large volumes of rapidly changing data (structured, semi-structured, or unstructured)
*   NoSQL databases can be pretty easily installed on cheap hardware clusters as transaction and data volumes increase
*   Quick schema iteration and frequent code pushes
*   Object-oriented programming
*   Data volumes that can involve many terabytes or petabytes
*   Data that is stored and managed in different locations or data centers

### Disadvantages Over RDBMS

NoSQL databases are not the best solution for all databases needs.  Some of the drawbacks of NoSQL databases are:

*   RDBMS is a lot older than NoSQL and is therefore mature (most RDBMSs have a lot of functionality and stability).  NoSQL systems are an exciting prospect but they are still relatively new.  Most of the disadvantages are a direct result of NoSQL being so new.
*   Similarly, NoSQL systems have less support.  NoSQL databases tend to be open-source and/or from small startup companies which lack resources to fully support 24/7.
*   Most business intelligence tools do not offer connectivity to NoSQL databases yet.
*   NoSQL was initially designed to offer a solution that required no administration.  However, this hasn't proven itself to be the case as NoSQL databases still demand a lot of technical skill with installation and maintenance.
*   Because it is so new, there is not the same level of expertise as you can find with RDMBSs.

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._