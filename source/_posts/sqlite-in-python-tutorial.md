---
title: SQLite in Python Tutorial
tags:
  - Automation
  - SQLite
  - Python
url: 755.html
id: 755
categories:
  - How To
date: 2016-02-23 08:00:06
---

In this tutorial, we will discuss how to connect to an existing SQLite database, creating a new SQLite database, and manipulating SQLite tables all using Python.  I will assume you already have and understand Python and SQLite.  If not, the official Python [documentation](https://wiki.python.org/moin/BeginnersGuide/Download) is a good place to start learning Python and my [introduction to SQLite](/an-introduction-to-sqlite/) should be enough for you to understand this tutorial.

### Connect to an Existing SQLite Database

In order to connect to SQLite, we will need to use the built in sqlite3 module and create a connection variable. \[python\] import sqlite3 conn = sqlite3.connect('C:\\\Users\\\Allison\\\pantrydb.db') c = conn.cursor() \[/python\]

### Create a New SQLite Database

Creating a new SQLite database is quite simple.  If the database you are trying to connect to doesn't already exist, SQLite will create it for us. \[python\] import sqlite3 conn = sqlite3.connect('testdb.sqlite') c = conn.cursor() \[/python\]

### Create a New Table

Next, we will create a table with one column: \[python\] c.execute('CREATE TABLE customer (customer_id INTEGER PRIMARY KEY AUTOINCREMENT, firstname VARCHAR(50) NOT NULL)') conn.commit() conn.close() \[/python\] Lets make sure this worked.  Open the command prompt (windows key + r) and type 'cmd'.  Next, type \[text\] sqlite3 pantrydb.db \[/text\] This will open your SQLite database.  Next, to see if the table was created, type: \[text\] .tables \[/text\] The 'customer' table should be listed below.  To make sure it is correct, check the schema by typing: \[text\] .schema customer \[/text\]

### Add a New Column with Variables

First, lets define our variables: \[python\] database = 'pantrydb.db' table = 'customer' id\_column = 'customer\_id' # name of the PRIMARY KEY column newcolumn = 'lastname' column\_type = 'VARCHAR(50)' \[/python\] Next, we will connect to the database and execute the ALTER TABLE statement.  To do this without a default value (default = NULL): \[python\] conn = sqlite3.connect(database) c = conn.cursor() c.execute("ALTER TABLE {tn} ADD COLUMN '{cn}' {ct}"\ .format(tn=table, cn=newcolumn, ct=column\_type)) \[/python\] To create a default value: \[python\] default\_val = 'Smith' conn = sqlite3.connect(database) c = conn.cursor() c.execute("ALTER TABLE {tn} ADD COLUMN '{cn}' {ct} DEFAULT '{df}'"\ .format(tn=table, cn=newcolumn, ct=column\_type, df=default_val)) conn.commit() conn.close() \[/python\]

### Manipulating Rows

Manipulating rows follows the same formula as above. \[python\] database = 'pantrydb.db' table = 'customer' id\_column = 'customer\_id' column = 'firstname' conn = sqlite3.connect(database) c = conn.cursor() try: c.execute("INSERT INTO {tn} ({idf}, {cn}) VALUES (NULL, 'Allison')".\ format(tn=table, idf=id\_column, cn=column)) except sqlite3.IntegrityError: print('ERROR: ID already exists in PRIMARY KEY column {}'.format(id\_column)) conn.commit() conn.close() \[/python\]   That is the bulk of using Python with SQLite!  This should be enough foundation for you to be able to more or less manipulate a SQLite database with Python!  If you have any questions or comments, feel free to let me know below or [contact me](/contact/).