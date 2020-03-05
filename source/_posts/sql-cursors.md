---
title: SQL Cursors
tags:
  - SQL Server
url: 1586.html
id: 1586
categories:
  - Methodology
date: 2016-07-26 08:00:46
---

In SQL, a **cursor** lets you process data on a row by row basis.  As we've seen, SQL is really good at manipulating data _sets_.  We can group many rows together, update many rows at the same time, delete many rows at the same time, etc.  However, instead of performing a single action on a set of rows we might want to loop through a list of rows and process along the way.

### Syntax

Below is the general syntax for a cursor:

DECLARE @AuthorID char(11)
	
DECLARE c1 CURSOR READ_ONLY
FOR
SELECT au_id
FROM authors

OPEN c1

FETCH NEXT FROM c1
INTO @AuthorID

WHILE @@FETCH_STATUS = 0
BEGIN

	PRINT @AuthorID

	FETCH NEXT FROM c1
	INTO @AuthorID

END

CLOSE c1
DEALLOCATE c1

The first DECLARE is defining a variable (@AuthorID).  Variables have to begin with @ and can be given a value in the declaration statement.  An example of this would be:

DECLARE @AuthorID char(11) = abc123def45

The next DECLARE is declaring the cursor.  This cursor has the name _c1_ and will be read only.  This is important - if we are only going to be _reading_ data, we want to be sure to set the cursor as read_only.  This will help us imporve performance.  The SELECT statement below it is the process that we want to use to loop through every row.  Note you **must** have the FOR statement before the SELECT statement. Next, the OPEN statement executes the cursor's SELECT statement and puts the result in a set. The FETCH command returns one row from the result and puts it into the @AuthorID variable.  We could fetch multiple columns and put them into multiple variables if we wanted (assuming we selected multiple in the cursor definition, of course!). Next, the line

WHILE @@FETCH_STATUS = 0

tells SQL that we want to continue doing this until there are no more rows.  In other words, FETCH_STATUS will be 0 until there are no more rows. Next, the lines within the BEGIN/END will loop through the @AuthorID variable and print each @AuthorID. Obviously, there are much more efficient ways to do this within SQL.  This example is just a basic one to get an idea of the syntax.  In general, if you can avoid using a cursor, you probably should because they can be so slow. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._