---
title: An Introduction To MongoDB
tags:
  - MongoDB
  - NoSQL
url: 887.html
id: 887
categories:
  - How To
date: 2016-03-29 08:00:00
---

Until now, all of my tutorials have dealt with relational database systems (SQL based databases).  MongoDB will be my first NoSQL system -- for more information on NoSQL, check out last week's post: "[What Is NoSQL?](/what-is-nosql/)".  I first heard about MongoDB on the [Talk Python To Me Podcast](https://talkpython.fm/episodes/show/2/python-and-mongodb).

### Why Choose MongoDB?

If you or your application falls into one of these categories, you might consider using or switching to MongoDB:

*   You expect a high write load (you need to write tons and tons of data lines)
*   You need high availability in an unreliable cloud based or real life environment
*   You expect to grow big
*   You don't have a DBA (though, if you are expecting to go really big, you should probably get a DBA)
*   You can't afford to spend a lot of time doing schema design

### Why Not Choose MongoDB?

MongoDB set out to solve the big data problem and it is a really good solution if you anticipate needing to grow fast.  However, it isn't the end-all-be-all database.  Keep these in mind when considering using or switching to MongoDB:

*   You can't use SQL.  This is obvious since MongoDB is a NoSQL database; however, if you think you're going to make mission critical decisions where you need multiple documents/collections, an RDBMS might be better
*   No automatic rollback, possibility of inconsistent reads, etc
*   It doesn't do well if several apps access it simultaneously
*   MongoDB was designed with modern storage and processing power and it isn't optimized to work on HDD.  It works best if your indexes fit into RAM and it is really ideal to have SSD hard drives in your production environment

If you are okay with all of the above, or if you are like me and just want to learn, follow the directions below to get started!

### Setting Up MongoDB

#### Installation

Go to the official [MongoDB website](https://www.mongodb.org/downloads#production) and click the Download MongoDB button.  I'll be downloading MongoDB 3.2.  Once the msi file downloads, follow the steps in the Setup Wizard to install MongoDB.![2016-03-12_20h14_50](/wp-content/uploads/2016/03/2016-03-12_20h14_50.png) When prompted, I chose the 'Custom' option so that I could define where MongoDB installs.  I also went ahead and added the installation directory to my Path Variable. If you open up the install directory, you will notice many executable files within the bin folder.  The ones we care about most are _mongo.exe_ and _mongod.exe_.  Mongo.exe is the shell and mongod.exe is the server.

#### Setting Up MongoDB Environment

MongoDB requires a data directory to store its data.  By default, it looks for this path to be **\\data\\db**.  Create this folder in whatever directory you want with the command prompt or the file explorer.  To tell MongoDB where this directory is, use the command: \[text\] path-to-mongod.exe\\mongod.exe --dbpath path-to-your-data-directory \[/text\] For me, this command was: \[text\] mongod --dbpath E:\\Dropbox\\Dropbox\\mongodb\\data\\db \[/text\] At this point, the command prompt should look something like this: \[text\] 2016-03-12T20:34:16.028-0500 I NETWORK \[initandlisten\] waiting for connections on port 27017 \[/text\] It seems like it is stuck waiting for the connection to open.  At first, I thought something was wrong (maybe it couldn't connect to my Dropbox or I set something up wrong).  However, this is actually a sign that the MongoDB server is up and running and it is listening to the port 27017.  Open another command prompt and type _mongo_ to open the shell.  In this new window, you should see something like: \[text\] MongoDB shell version: 3.2.4 connecting to: test > \[/text\] If you check out our original shell, you will see a new line has been added: \[text\] 2016-03-12T21:00:29.034-0500 I NETWORK  \[initandlisten\] connection accepted from 127.0.0.1:56222 #1 (1 connection now open) \[/text\] While in the shell, you can type 'help' to get a list of available commands and their descriptions.

#### Documents

MongoDB is fundamentally different than SQL based database systems so we need to define a couple of things before we attempt to add anything to our database.  First of all, a record in MongoDB is called a **document**.  This document is a data structure composed of field/value pairs.  They are similar to JSON objects.  For example, a document might look like this: { name: "Allison", age: 26, occupation: "engineer", hobbies:\["coding", "speedsolving"\] } The above document has four **field/value pairs**.  The fields are: name, age, occupation, and hobbies; the values are: "Allison", 26, "engineer", and \["coding", "speedsolving"\].

#### Basic Operations

##### Insert

Now that we have a little understanding of what is inside a document, we can create one!  To do this, we use the _insert()_ command.  I want to create a **collection** called _names_.  A collection is a group of documents. \[c\] db.names.insert({'name':'Allison Tharp'}); \[/c\] This will return \[text\] WriteResult({ "nInserted" : 1 }) \[/text\] This tells us that one line was inserted to the database.  To see everything in the _names_ collection, type: \[c\] db.names.find() \[/c\] For me, this returns: \[text\] { "_id" : ObjectId("56e4ca3cb6dda09b54eeb04c"), "name" : "Allison Tharp" } \[/text\] We can add another document to this collection by typing the same thing: \[c\] db.names.insert({'name':'Joe Schmo'}); \[/c\]

##### Remove

Lets remove that collection so that we can add something a little more complicated.  To remove all documents meeting some criteria, we can use the command: \[c\] db.names.remove({"name":"Joe Schmo"}) \[/c\] It is a good time to note that commands are case sensitive!  To delete the entire collection, we use the command: \[c\] db.names.drop() \[/c\]

##### Insert (again...)

Lets add the example document from above to a collection called names. \[c\] db.names.insert( { 'name':"Allison", 'age':26, 'occupation':'Engineer', 'hobbies':\["coding","speedsolving"\], 'address':{ 'street':'123 abc ln', 'city':'star city', 'state':'NY', 'zipcode':'12345' } }); \[/c\] Notice how I put a document (address) inside a document!  This is totally fine with MongoDB.  We call this a **nested document**.

##### Find

The find() command is used to retrieve data from a collection.  Note that all queries within MongoDB are limited to a single collection.  We've already used the find() command once!  In order to return all documents in a collection, we use the command: \[c\] db.names.find() \[/c\] where _names_ is the collection name.  If we want to query by a field value, use: \[c\] db.names.find({"age":26}) \[/c\] It is possible to search a nested document by using _dot notation_.  Dot notation _requires_ quotes around the entire dotted field name.  An example for us would be: \[c\] db.names.find({"address.city":"star city"}) \[/c\] **Operators** To use greater than within a value, use _$gt_.  For example: \[c\] db.names.find({"score":{$gt: 30}}) \[/c\] Similarly, less than is _$lt_. \[c\] db.names.find({"score":{$lt: 30}}) \[/c\] Logical AND simply combines multiple key/value pairs separated by a comma: \[c\] db.names.find({'age':26, 'city':'star city'}) \[/c\] Logical OR requires $or before the criteria: \[c\] db.names.find({$or: \[{'age':26}, {'city:'star city'}\]}) \[/c\] Notice for Logical OR we put the or statement inside curly brackets, {}, and the criteria is inside hard brackets, \[\]. **Sort Results** In general, when we use the sort command 1 is for ascending and -1 is for descending.  For example: \[c\] db.names.find().sort({"name":1}) \[/c\]

##### Update

The final basic operator is the Update command.  We use update() to update documents in a collection.  The acceptable input parameters are:

*   a filter document to match the documents to update
*   an update document to specify the modification to perform
*   an operations parameter (optional)

The general form for update() is: \[c\] db.collection.update( {'searchkey':'searchvalue'}, { $set:{'key\_to\_set':'new_value'} } ) \[/c\] For me, I can use: \[c\] db.names.update( {'name':'Allison'}, {$set:{'occupation':'software engineer'}} ) \[/c\] This changes my occupation in the document from _engineer_ to _software engineer_. This is a really quick introduction to MongoDB.  Obviously there is a lot more to this database system than what we discussed here.  However, I think with these tools you should be able to set up a database and begin discovering all that MongoDB has to offer.

### Final Thoughts

Researching for this blog post was my first time being exposed to NoSQL and MongoDB.  I currently feel like MongoDB is pretty cumbersome -- it takes a **lot **more typing to do the same things MySQL or SQLite could do with very little syntax.  I'm not used to JSON, so seeing data presented in these collections and documents isn't intuitive to me.  However, I can also see the benefits of it.  Python works well with key/value pairs so I think working with MongoDB from Python could be really easy and powerful.  I'm always excited to add another tool in my toolbox and I feel like going further in depth with learning MongoDB will certainly help me with many things in the future, even RDBMS systems. 

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._