---
title: 'SQL Tips & Tricks #2'
tags:
  - SQL Server
url: 1691.html
id: 1691
categories:
  - Methodology
date: 2016-09-02 08:00:01
---

I'm still learning a lot about SQL and I want to be sure I keep track of all of the neat little tricks I find out about along the way.  My _SQl Tips & Tricks_ series isn't on a regular schedule; rather, once i feel like I have enough new tips and tricks, I'll gather them up and post them here.  You can check our my first SQL Tips & Tricks post [here](/sql-tips-tricks-1/).  

Transactions
------------

Transactions are a way to encapsulate a group of SQL lines to 'test', without worry that the commands will permanently affect the database.  In other words, transactions allow us to have an _undo_ button.  This is one of the biggest tips I've learned recently, and I always use them when working in a production environment (better safe than sorry!).

### Why?

Selects do not alter your database, so you can use SELECT statements even on the most sensitive of databases without worrying too much about messing up the important data.  However, what if you want to update tables and check to make sure everything worked correctly (with select statements) and then 'undo' the work if it wasn't correct?  Enter transactions!

### How?

Transactions are super simple.  Before the code that you want 'protected', simply put the line (**SQL Server** syntax):

BEGIN TRANSACTION

You can then put all of your updates, deletes, selects, whatever.  If you decide the changes you made were incorrect, simply use the command:

ROLLBACK TRANSACTION

If everything was perfect, you can commit the transaction with:

COMMIT TRANSACTION

If you close your instance of SQL Server, the transaction will be rolled back.  

@@Trancount
-----------

This is related to the last tip, so this will be short.  If you want to know how many transactions you currently have _open_ (in other words, not rolled back or committed), you can use the command:

SELECT @@trancount

Table Variables
---------------

After learning about table variables a few weeks ago, I now use them all of the time.  They are awesome if you have a list of items (such as IDs) that you know you will need to look through.  Table variables are very similar to temp tables, which I discussed in the first Tips & Tricks.

### Why?

You use table variables for many of the same reasons you would use a temp table.  However, in general, if you have a small(ish) amount of data, and if you want to 'hard code' in your data, a table variable might be better for you than a temp table. In addition, table variables aren't affected by transactions.  Since table variables are not real tables but instead are variables, if you rollback part of the way through a procedure, the table variables will still be populated (unlike if you use a temp table). I also find it easier to 'hard code' my data into table variables, and I don't worry have to worry about creating/dropping/editing the wrong table (since it isn't really a table).

### How?

**SQL Server **syntax:

DECLARE @tablename table (col\_1 int, col\_2 varchar(30))

INSERT INTO @tablename VALUES
(1, 'one'),
(2, 'two')

SELECT * FROM another\_table WHERE some\_id IN (SELECT id FROM @tablename)

 

Latest Row Inserted
-------------------

### Why?

In **SQL Server**, there are many ways to get the primary key of the most recently inserted row.  This can be very useful if you are doing many inserts and updates in a single SQL script and don't want to have to do a SELECT statement to try to get the ID of the row you just inserted (which could result in the incorrect ID).

### How?

There are at least three ways to do this in **SQL Server**: **@@Identity** This gets the last value generated for any table (**in your current session and across all scopes**).  _Be very careful with this!_  This system variable (we know it is a system variable because it begins with @@) will get whatever the last value generated was.  That means if you insert a row into table A, but there is a trigger on table A that inserts a row into table B (and table B has a generated value associated with it), the output will be the value associated with table B.  This might be exactly what you want, but in most cases, you will probably want the value associated with table A, so be careful! **SCOPE_IDENTITY()** This is a system function that is very similar to @@Identity, except it only returns the last generated value for any table **in your current session and in the current scope**.  The scope\_identity() function is not affected if a trigger or user defined function inserts into another table.  However, if there is a trigger/user defined function that affects the same table, the scope\_identity() function will return the identity of the row that was created by the trigger/function. **IDENT_CURRENT()** This function is not affected by scope or session.  That means it will return the identity value that is generated on a particular table.  This is the one I tend to use most often, as I usually want the ID for the table I am the one inserting into.   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._