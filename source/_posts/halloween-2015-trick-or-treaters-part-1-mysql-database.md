---
title: 'Halloween 2015 Trick or Treaters Part 1: MySQL Database'
tags:
  # - Halloween2015
  - MySQL
url: 7.html
id: 7
categories:
  - Personal Projects
  - Halloween
date: 2015-11-05 19:33:44
---

I got the idea the night before Halloween this year that I could make a GUI and MySQL database to track our trick or treaters.  First, I laid out everything I thought I wanted to track:

*   size of group split out between number of adults and number of kids
*   the time the group arrived
*   the costumes that the kids and adults were wearing
*   what candy each kid chose (we were the cool house with full size bars!)

From there, I started designing the schema.  I work best with a white board so I first drafted the tables and columns on the whiteboard in my office and then transferred it to MySQL Workbench EER Diagram.  I had never used the [MySQL Workbench software](https://www.mysql.com/products/workbench/) before but I found the EER Diagram portion of it to be self explanatory. A **schema** can mean a lot of things in the world of databases depending on the database software you are using and the people you are talking to.  In general though, the schema is how your database is laid out.  A common way to illustrate the schema is with a diagram like the one below.  Typically, each table will be represented with a box.  Within each box contains each of the columns of that table.  I prefer the primary key to be the first item listed, though this isn't strictly necessary (more on primary keys in a bit). You may notice the lines connecting the boxes in the diagram.  These lines represent the type of relationship between each table and can be very useful to you when you start actually making the tables in MySQL.  The three general types of relationship are:

*   one-to-one
*   one-to-many
*   many-to-many

This is a large topic that I will cover in another post.  For now however, just know that if two tables are in a one-to-one relationship the line connecting them will simply be one straight line.  However, in a one-to-many relationship (which all of these tables are) one end of the relational line will have just one end to it and the other end will branch out to have multiple points. For instance, the end of the line touching the kcostume table has just one point but it branches out to many points when it goes to the kid table. The final schema is shown below: \[caption id="attachment_15" align="aligncenter" width="449"\][![2015-11-05_19h18_16](http://www.techtrek.io/wp-content/uploads/2015/11/2015-11-05_19h18_162-449x304.png)](http://66.172.33.213/wordpress/wp-content/uploads/2015/11/2015-11-05_19h18_162.png) Halloween Trick or Treaters Schema\[/caption\]   'group' is the main table for each group of trick or treaters that arrived.  This table includes the number of adults (no\_adults), number of kids (no\_kids), the time they arrived (gtime), as well as the group id (group\_id).  The group\_id was the **primary key**, notated in MySQL Workbench by a yellow arrow.  For SQL databases, each table should have a column dedicated to the primary key.  This key should be unique for every single entry in the column.  I typically have this be a non null, auto-incrementing integer, though it could be anything.  Primary keys give you the ability to distinguish each entry and they can also be used if you want to reference the value in another table. The 'kid' table included the kid primary key (kid_id) and three **foreign keys**: costume\_id, candy\_id, and group\_id.  The foreign keys are notated in the Workbench as a red diamond.  Foreign keys are a place holder for referencing the primary key in another table.  For instance, each group that came to my house had one row in the 'group' table and a unique group\_id.  Each kid that came to my house had his or her own row in the 'kid' table with his or her own unique id as well.  In order to track which kid was with which group, I implemented a foreign key group\_id.  This entry for each kid would be the unique group number for the group the child arrived in. The 'adult' table works the same way as the 'kid' table, except the adult's weren't taking any candy so there is no candy\_id foreign key!  The remaining tables ('acostume' which holds the unique adult costumes, 'kcostume' which holds the unique kid costumes, and 'candy' which holds the unique candy bars we had) are all very simple.  Each entry has its own unique id (its foreign key) and only one other column: the name of the costume or candy.  Each of these tables were set up such that there could be no duplicate entries. To see all of the entries in the 'candy' table, simply select all: _select * from candy;_

**candy_id**

**candy_name**

10

8

funsize

5

skittles

1

snickers

7

sour_skittles

2

starburst_orig

3

starburst_tropical

4

toblerone

6

wildberry_skittles

This was the easiest table to set up because I knew exactly what the options were for the kids to choose from and once it was set, I knew it wouldn't have to be added to. That's all for this portion of my Halloween Trick or Treaters project.  Part 2 will cover the GUI design process and how I put it all together.