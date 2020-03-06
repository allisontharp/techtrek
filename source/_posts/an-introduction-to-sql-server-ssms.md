---
title: An Introduction To SQL Server - SSMS
tags:
  - Administration
  - SQL Server
url: 1112.html
id: 1112
categories:
  - How To
date: 2016-04-15 08:00:50
---

In this tutorial, I will assume you already have SQL Server Management Studio (SSMS) up and running and that it is already connected to your SQL Server.  Check out Tuesday's post [here](http://www.techtrek.io/installing-sql-server-on-windows-server-2016/) if you haven't set those up yet.  In this tutorial, we will go over SQL Server Management Studio basics.  It would probably be good if you had a basic understanding of SQL but that isn't required.

Creating A Database
-------------------

In the Object Explorer on the left side of the window, you will notice a tree.  The top item in the tree should say Databases, like this: ![Screenshot 2016-04-12 at 4.47.41 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-12-at-4.47.41-PM.png) To create a new database, right click the word **Databases** from the Object Explorer and click **New Database**.  A new window, which should look like the image below, will open: ![Screenshot 2016-04-12 at 4.54.19 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-12-at-4.54.19-PM.png) There are a lot of options we can set for our database with this window.  The only thing we really have to provide is a database name (in the first text box).  Other than that, we will keep everything else as their default values.  We'll explore some of these options in later posts. I'm going to recreate my Board Game Play Tracking database, since this is something I've already done a few times (once with [Google Sheets](http://www.techtrek.io/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/) and another time with [SQLite](http://www.techtrek.io/board-game-play-tracking-using-sql-part-2-building-db/)).  I'll put my database name, **BGPlays**, in the Database Name prompt.  You'll notice as you type that SSMS automatically creates two database files, one with the name of your database (in my case, BGPlays) and one with your name\_log (BGPlays\_log).  I'll keep both of these as they are. On the left, you'll notice three pages: **General**, **Options**, and **Filegroups**.  As we have already seen, the General page deals with your database name but also for things like the initial size of the database and how SQL Server will manage its growth.  We could also change where it is located, but we will leave all of this as is.  The next two pages deal with more gritty details of the database, which we will learn about in future blog posts.

### Adding Tables

We have our database, so now we need some tables.  Doing this is very similar to creating a new database.  Go to the Object Explorer and expand your database.  You should see an option for **Table**.  Right click that and choose New -> Table. ![Screenshot 2016-04-12 at 8.56.22 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-12-at-8.56.22-PM.png) This will open up a couple of new windows, which are more or less broken up into three parts.  They are shown below: ![Screenshot 2016-04-12 at 8.57.24 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-12-at-8.57.24-PM.png) The main part is on the top left.  Here, we can see and add the fields for this table.  We have the options **Column Name**, **Data Type** (which can be things like INT, VARCHAR, timestamp, etc), and **Allow Nulls**.  The first table I'm going to create will be my _games_ table.  This table will have the columns game\_id (which will be the unique ID for the table), name (the game name), and bgg\_id (the id for the game in boardgamegeek).  All of these will be required except for bgg_id.  My fields in the end look like this: ![Screenshot 2016-04-12 at 9.04.58 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-12-at-9.04.58-PM.png) The next portion of the window is just below this, on the bottom left side, and is titled **Column Properties**.  This section lets you set and change settings for specific columns.  This is divided into two parts: _general_ and _Table Designer_. ![Screenshot 2016-04-12 at 9.07.20 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-12-at-9.07.20-PM.png) General contains the same information as what we have already set; however, here is where you would set a Default Value if you needed.  Some of these properties will depend on the Data Type that you have chosen.  For this basic database, the only thing we care about is under Identity Specification.  Click the on the column for your primary key and then click the arrow net to **Identity Specification** in the Column Properties panel.  It will look like this: ![Screenshot 2016-04-14 at 4.55.49 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-14-at-4.55.49-PM.png) To let SQL Server know which column we want to be the primary key, we must change the **(Is Identity)** property to _Yes_.  If we want this column to auto increment, keep the **Identity Increment** value at _1**.**_ The third and final section is on the right and is title **Properties**.  This portion deals with the table and not the individual columns.  For now, all we will do is give the table a name (for me, this is _games_) and tell it which column to use for our Primary Key. ![Screenshot 2016-04-14 at 5.14.22 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-14-at-5.14.22-PM.png) Before we save, we need to tell SSMS that we are okay to save changes that require a table to be re-created (otherwise, when we define the primary key column we will get an error).  To do this, navigate to: **Tools** -> **Options** -> **Designers** -> **Table and Database Designers**.  From here, clear the **Prevent saving changes that require the table to be re-created**.  After this, save the table and database by pressing Ctrl+Shift+S or go to File -> Save All. I will repeat this process for the rest of my tables (_locations_, _players_, _scores_, _plays_, and _playersplays_).  In order for our tables to show up in the Object Explorer, right click on the database name and click **Refresh**.  If you want to get back to the view that we have when we create a new table, right click the table and choose **Design**.

### Insert Data

To insert data, click the **New Query** button, which is near the top of SSMS and in the middle.  It looks like this: ![Screenshot 2016-04-14 at 6.02.59 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-14-at-6.02.59-PM.png) Alternatively, we can open the query window by pressing Ctrl+N.  Within this window, we can type our SQL commands to manipulate the data.  To insert data into our database, we use the following general formula:  

INSERT INTO dbname (column1, column2) VALUES (value1, value2)

Alternatively, if we put the data in the same order that we defined the table in, we can simplify it as:  

INSERT INTO dbname VALUES (value1, value2)

If you are familiar with other versions of SQL (such as SQLite or MySQL), there is one major difference.  With other languages, we must specify our auto incrementing column and give it the value NULL.  With SQL Server, we do not put anything for that column.  For example, my _games_ table has the columns game\_id, name, and bggid.  The game\_id is the primary key column and it is auto incrementing.  The command to insert a row into this table is different for SQLite/MySQL and SQL Server. **SQL Server:**

INSERT INTO games VALUES ('Alhambra', 6249)

  **SQLite/MySQL:**  

INSERT INTO games VALUES (NULL, 'Alhambra', 6249)

Once we type our command, we can run it by pressing F5 or by clicking the **Execute** button.

### Visualize Data

SSMS allows us to view the last 200 rows of a table without using any SQL Commands.  To do this, right click on the table and choose **Edit Top 200 Rows**. ![Screenshot 2016-04-14 at 6.00.18 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-14-at-6.00.18-PM.png) This will bring up a new window that looks like this: ![Screenshot 2016-04-14 at 6.18.11 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-14-at-6.18.11-PM.png) Alternatively, we can use the Query window to view our data.  The command for this is just like it would be for SQLite or MySQL:  

SELECT * FROM dbname

For me, this returns: ![Screenshot 2016-04-14 at 6.20.56 PM](/wp-content/uploads/2016/04/Screenshot-2016-04-14-at-6.20.56-PM.png) That's it for building our first SQL Server database using SSMS. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._