---
title: 'SQL Tips & Tricks #1'
tags:
  - SQL Server
url: 1605.html
id: 1605
categories:
  - Methodology
date: 2016-07-29 08:00:32
---

I've been working and learning more about SQL a lot lately and I have found some pretty cool tips and tricks.  I thought it would be good to start documenting some of these in a place where I could find them later.  I assume there will be more posts like this in the future.

SELECT INTO #tempTable
----------------------

This is a trick that I am really, really excited about.  This trick allows you to select data as you normally would but instead of just outputting the result into the result window, you can actually dump the data into a temporary table.

### **Why?**

There are many benefits to a temporary table.  A big benefit here is that it can help you simplify your very complicated queries.  For instance, it might be easier to do a large portion of your query in one select statement, dump it into a temp table, then do the groupings/sorting/etc from there.

### **How?**

The general syntax within **SQL Server** for this is:

SELECT *
INTO #tempTableName
FROM
    ....

For **MySQL** or **SQLite**:

CREATE TEMPORARY TABLE IF NOT EXISTS tempTableName AS
(SELECT * FROM .... )

This temporary table will work just as your other tables do.  If you want manually get rid of the table:

DROP TABLE #tempTableName

ISNULL()
--------

The ISNULL() function checks to see if a specified expression is null.  If it is, it replaces the value (just in the output) with another value.

### **Why?**

If you know you have some null values in a result, but you want to see some other value, this is the easiest way to do it.

### **How?**

Within **SQL Server**:

ISNULL (expression\_to\_check, replacement_value)

Note, the **ISNULL() **function exists within **MySQL** and **SQLite** but it behaves differently.  The syntax for **MySQL/SQLite** is:

ISNULL(expression)

If the expression is null, the result is 1.  Otherwise, it is 0.  To get the same behavior as SQL Server, the syntax for **MySQL/SQLite** is:

IFNULL(expression\_to\_check, replacement_value)

CASE
----

This function works just like it does in traditional programming.  For the CASE function, we compare values to determine an output.

### **Why?**

You might use this for the same or similar reasons as an 'if' statement in programming.  If your output will depend on some other value, a CASE statement may be needed.

### How?

**SQL Server/MySQL/SQLite** syntax:

SELECT 
    CASE
        WHEN expression THEN output
        WHEN expression2 THEN output2
    END
FROM ...

That's it for the first version of Tips and Tricks! _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._