---
title: 'Functions, Stored Procedures, and Views'
tags:
  - MySQL
  - SQL Server
  - SQLite
url: 1737.html
id: 1737
categories:
  - Methodology
date: 2016-09-20 08:00:58
---

In RDBM systems, there are many ways to write SQL code that is reusable.  Three main ways are _views_, _functions_, and _stored procedures_.

### **Views**

Views are very similar to tables except they contain (potentially complex) calculations and joins.  Because of this, they can hide complexity and make future select statements easier.  They are really powerful if you often need to join the same sets of tables (like Customer and Customer Detail). Views can also be used as a security measure.  For instance, you may select only certain columns/rows from a table and set specific permissions to the view so that the user only sees the data that they 'need' to see. During my research of views, they seemed pretty much identical to temp tables (which we discussed [here](/sql-tips-tricks-1/)).  In general, views are short cuts for select statements.  The results have to be regenerated each time a view is used, unlike temp tables.  Therefore, if the query is 'big', you might be better off using a temp table.

### Functions

In general, you can do more with functions than you can with views but they also use more resources.  Views can almost always be converted into a function but functions can't always be converted into views.  You can use functions in views, stored procedures, and other functions.  In general, you can't change data with functions and they usually can't take output arguments that are then passed back out with changed values. You can't pass variables into views, but you can with functions.  Additionally, you can use functions that return scalar values in line with your SQL statements or join on the output if the result is a set. My general feel for functions is that they are more of a programming function (like what you would define in Python) where as views are more like a temporary table to store data while you perform more complex tasks.

### Stored Procedures

Stored procedures (commonly called 'stored procs'), are the most powerful of each of these.  Stored procedures can have transactions built in, they can have input and output parameters, and they can call functions.  However, you can't use them within SELECT statements. A stored procedure is almost a combination of functions and views -- they are a powerful way to write reusable code that can take parameters and output them in a variety of ways.  Stored procedures are almost like an individual program or a batch script as opposed to a traditional function.   I'm excited to start writing more views, functions, and stored procedures.  I'm also excited to start thinking in these terms.  One of the best qualities of a good developer, in my opinion, is the ability write reusable code (and know when not to!). _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._