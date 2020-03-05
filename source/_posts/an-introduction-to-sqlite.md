---
title: An Introduction To SQLite
tags:
  - SQLite
url: 625.html
id: 625
categories:
  - How To
date: 2016-02-09 08:00:02
---

SQLite, like MySQL, is a relational database management system (RDBMS).  With SQLite, your entire database is a single file on your local disk, meaning it is really portable.  However, it doesn't have network capabilities built in.  Even though SQLite uses SQL, it is missing some basic SQL commands such as RIGHT OUTER JOIN and FOR EACH but it also has others built in.  Additionally, SQLite doesn't have user management.  You'll want to use SQLite if you want an embedded application or if you want your database to be really portable and you'll want to avoid it if you are using it for a multi-user application or if you need a complicated database.

##### Installing SQLite

To install SQLite, go to the official [SQLite download page](http://www.sqlite.org/download.html) and download the **precompiled binaries for Windows**: the dll and the SQLite tools.  Create a folder where you'd like SQLite to live.  For me, I chose _E:\\sqlite_.  Unzip the precompiled binaries to this folder.  Next, add that path to your PATH environment variable.  Test to make sure this worked by opening your command prompt and typing

sqlite3

You should receive something like:

SQLite version 3.10.2 2016-01-20 15:27:19
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.

##### Creating A Database

If you just tested your environment variable by typing 'sqlite3' into your command line, go ahead and quit sqlite by typing .quit.  In order to create a database with SQLite, within the command line 'cd' to the directory where you'd like the database file to live and type: sqlite3 databasename I'm going to recreate my [firearms database](http://www.techtrek.io/gunsmith-db-and-app-part-1-overview/), so I want to create a database called 'firearms':

sqlite3 firearms

##### Creating A Table

Now, we can create a table.  The first table I'll create will be called _acquisition_.  This table will need the columns: \[table caption="Acquisition Table" width="500" colwidth="20|100|50" colalign="left|left|center|left|right"\] Field, Type, Null, Key, Default, Extra acq\_id, int, no, primary, null, auto increment acq\_date, date, no, , null, customer\_id, int, no , , null, gun\_id, int, no, , null, transaction\_date, date, no, , null, date\_entered, timestamp, no, , current time, purchase_price, float, yes,, null, \[/table\] Some of the syntax will be different with SQLite than it is with MySQL.  To create this table with SQLite, the command is:  

CREATE TABLE acquisition(
 acq_id INTEGER PRIMARY KEY AUTOINCREMENT,
 acq_date DATETIME NOT NULL,
 customer_id INTEGER NOT NULL,
 gun_id INTEGER NOT NULL,
 transaction_date DATETIME NOT NULL,
 date\_entered DATETIME NOT NULL DEFAULT CURRENT\_TIMESTAMP,
 purchase_price FLOAT);

Note that _INTEGER_ needs to be spelled out and to auto increment we use AUTOINCREMENT instead of MySQL's syntax of auto_increment.  To see the tables in our database, use:

.tables

The output for me is:

sqlite> .tables
acquisition

To see the schema for a table, type:

sqlite> .schema acquisition
CREATE TABLE acquisition(
acq_id INTEGER PRIMARY KEY AUTOINCREMENT,
acq_date DATETIME NOT NULL,
customer_id INTEGER NOT NULL,
gun_id INTEGER NOT NULL,
transaction_date DATETIME NOT NULL,
date\_entered DATETIME NOT NULL DEFAULT CURRENT\_TIMESTAMP,
purchase_price FLOAT);

Inserting values into a table is very similar in SQLite and MySQL.  I have a table called 'caliber' that has a schema:

sqlite> .schema caliber
CREATE TABLE caliber(
cal_id INTEGER PRIMARY KEY AUTOINCREMENT,
name VARCHAR(50) NOT NULL);

to insert a value into this table, we can use:

INSERT INTO caliber VALUES (NULL, '10ga');

Finally, to see all of the values in a table:  

sqlite> SELECT * FROM caliber;
1|10ga

In order to delete a row from a table, we can use:  

sqlite> delete from caliber where cal_id = 1;

And checking to make sure it did delete:

sqlite> SELECT * FROM caliber;
sqlite>

This should be enough for you to get your SQLite database up and running with some tables and data.  If you have any questions, please let me know in the comments or [contact me](/contact/).