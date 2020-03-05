---
title: Board Game Inventory In SQL Server
tags:
  - boardgames
  - SQL Server
url: 1563.html
id: 1563
categories:
  - Personal Projects
  - Boardgames
date: 2016-07-22 08:00:52
---

I feel really confident with MySQL and SQLite.  I need to practice more with Microsoft's enterprise level software (SQL Server), so I thought I would rebuild my Board Game Inventory database in SQL Server. It looks like there is an [official Microsoft tool](https://msdn.microsoft.com/en-us/library/hh313129(v=sql.110).aspx) to migrate a database from MySQL to SQL Server.  I will eventually use this tool to pull in all of the data; for now, however, I'm interested in using SSMS and practicing building a database "from scratch". I will assume you already have SQL Server installed on a Windows Server machine.  If not, check out my tutorial on that [here](http://www.techtrek.io/installing-sql-server-on-windows-server-2016/).

### Open SQL Server Management Studio

When you log into Windows Server, the desktop should look like the image below.  For me, SQL Server Management Studio (SSMS) is on the tray at the bottom.  It looks like a database icon with a settings icon (wrench and hammer).

### ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-1024x664.jpg)

It might take a few moments to load.  When it does, you will be prompted to connect to a server.  My server is named _WIN-BBPQPLSCOMB\\SQLSERVER2_.  I change to that server and click Connect: ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-1-1024x662.jpg)

### Create a New Database

To create a new database, add tables, and add data, we can follow the tutorial I previously wrote [here](http://www.techtrek.io/an-introduction-to-sql-server-ssms/). Some notes here that weren't in that tutorial: **A _composite key_** is a primary key that includes more than one column.  For the _gameupc_ table, I needed the primary key to be a combination of bggid and the upc.  To do this, select both columns (by holding Shift), right click, and select _Set Primary Key_: ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-3.jpg)   **To make a column unique**, right click on the column and choose Indexes/Keys...: ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-7-300x271.jpg) Click 'Add' in the lower left corner and change 'Is Unique' to Yes: ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-8-1024x647.jpg)   **To set a foreign-key relationship**, go to Table Designer - Relationships.. ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-4-279x300.jpg) The Foreign Key Relationships box will open.  Click 'Add' on the bottom left.  Next, click the "..." box to the right of "Tables and Columns Specification": ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-5-1024x651.jpg) This opens the Tables and Columns window.  Here, we specify which columns from which tables will be linked together.  For me, I want the playerid in the _checking_ table to be linked to the playerid in the _players_ table.  The end result of that looks like this: ![Capture](http://www.techtrek.io/wp-content/uploads/2016/07/Capture-6.jpg)   **Cannot insert explicit value for identity column in table '<tbname>' when IDENTITY_INSERT is set to OFF.**  I got this error on several tables when trying to insert data where I defined the identity value (this happens for many of the tables because I am using the BGGID for the primary key).  To fix this, run the command:

SET IDENTITY_INSERT <tbname> ON
GO
INSERT INTO ....

This only needs to be done for the first insert.  After the IDENTITY_INSERT is set to on, it will stay on.   **To set the default value of a datetime column to right now**, click on the column.  In the Column Properties window, set the Default Value or Binding to **getdate()**.   **Updating** **a row** follows the same syntax as MySQL and SQLite:

UPDATE <tablename>
SET <columnname> = <new value>
WHERE <some\_column> = <some\_value>

  **To comment** a line in the New Query window, use "--".   Rebuilding the database tables and adding some data in SQL Server was pretty easy.  Next, I need to look into programmatically adding the rest of the data. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._