---
title: 'SQL Tips & Tricks #3'
tags:
  - SQL Server
url: 1788.html
id: 1788
categories:
  - Methodology
date: 2016-10-11 08:00:18
---

I have a lot to learn.  I know that this is particularly true when it comes to SQL, so I try to note down neat things I learn about SQL and add them to one blog post when I have enough.  If you missed the previous ones, you can view them [here](/sql-tips-tricks-1/) and [here](/sql-tips-tricks-2/).

SET NOCOUNT ON
--------------

SET NOCOUNT ON prevents the message that shows the number of rows affected by a statement (or stored procedure) from being returned in the Results window.  The number of rows affected is still returned to the client, but the count is marked as not to be used.

### Why?

If you have a stored procedure that contains a lot of loops, turning this ON may provide a performance boost (and it could be significant).  Microsoft encourages SQL developers to use _SET NOCOUNT ON _in stored procedures.

### How?

This one is pretty easy:

SET NOCOUNT ON

TRUNCATE
--------

The TRUNCATE TABLE command removes all of the rows from the specified table.

### Why?

One case scenario for this might be if you have a table in database A that will be used for an EDI process in database B (or in application B).  You might import your data into a table that then gets some procedures called on it to pull all of the data into its respective tables and rows.  Once the process is done, you can TRUNCATE the original table so that it will be ready for the next batch of data.

### How?

TRUNCATE TABLE your_table;

CASE
----

Case statements are common in many programming languages (it is worth nothing that [Python](/tag/python/) lacks Case statement functionality).  A case statement is very similar to an if statement.  Case statements can be nested inside select statements, which make them really powerful!

### **Why/How?**

There are **many** reasons you might use a case statement.  Say you have a table with the columns PaidStatus (bit) and Amount (float).  If you want to get the total paid and the total unpaid, it might be pretty tricky to do.  However, CASE statements make this task pretty easy:

SELECT
    SUM(CASE WHEN PaidStatus = 0 THEN Amount ELSE 0 END) AS TotalUnpaid,
    SUM(CASE WHEN PaidStatus = 1 THEN Amount ELSE 0 END) AS TotalPaid
FROM
    ...the...rest...of...your...code...

Another reason you might want to use a Case statement is to modify the output of your select statement to be more 'human readable'.  For instance, you could have a CASE statement like this:

SELECT car_id,
       CASE WHEN mileage > 100000 THEN 'over 100,000'
            WHEN mileage > 50000  THEN '50,000 - 100,000'
            WHEN mileage > 10000  THEN '10,000 - 50,000'
            ELSE 'Below 10,000' END AS mileage
FROM
        ....

This SQL statement would allow you to select mileages as human readable amounts such as 'over 100,000'.  This could be helpful for building reports or sometimes even just understanding the data for yourself.  Also note that in this case statement, we added an ELSE.  If the mileage (which is a column in your table) does not fall into any of the other case statements, it will default to the ELSE statement.   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._