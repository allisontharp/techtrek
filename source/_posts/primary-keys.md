---
title: Primary Keys
tags:
  - MySQL
url: 24.html
id: 24
categories:
  - Methodology
date: 2015-11-06 21:35:16
---

I briefly described primary keys in the Halloween 2015 Trick or Treaters Part 1: MySQL Database post ([here](http://66.172.33.213/wordpress/index.php/2015/11/05/halloween-2015-trick-or-treaters-part-1-mysql-database/)).  Primary keys are a big subject in database design so I wanted to delve a little deeper into them. In case you missed the first post, every table in a database should have a **primary key** column.  This column can be made up of nearly anything, though I have found that the easiest and safest way is to have the entry be a not null, auto-incrementing integer. Think of primary keys as the social security number for each entry in a table.  Every entry must have a unique social security number so that if you ever need to reference the entry in the future, you will have a unique identifier for it. Though not all MySQL engines require you to specify a primary key, it is **always** a good idea to do so. If in the future you decide you want or need to do a _JOIN _or reference the entries by using a foreign key, you will have to retroactively add primary keys.  For larger tables this could be an issue! If you have reason to believe you do not need primary keys for your table you may want to rethink your database design.  If you do not need primary keys it would suggest you are keeping duplicate records which in most cases is not a good idea! To create a table with a primary key in MySQL, we use the command:

`CREATE TABLE [table_name] ([primary_key] INT NOT NULL PRIMARY KEY AUTO_INCREMENT);`

I will try to be consistent with how I write SQL queries.  A standard in the database world is that SQL commands are written in caps and table names, column names, etc are in lower case.  Most systems are not case sensitive but it is a good idea to get into this habit. I will always write SQL queries in code blocks and for general queries, such as the one above, anything inside square brackets are where you can place your own names.  Do not use the square brackets yourself!  For instance, the real SQL query for the above example would be: `CREATE TABLE kid (kid_id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,` `first_name VARCHAR(20));` This query would generate a table with the name 'kid' with the columns 'kid\_id' and 'first\_name'.  Each time you enter a new row into this table, kid\_id will increment by one (starting at 0). The primary key does not have to be a not null auto-incrementing integer.  Instead, it could be the combination of multiple columns in the table.  For instance, if you have a table of students with columns first\_name, last\_name, and gender the primary key could be the concatenation of first\_name and last_name.  However, this could result in two identical primary keys so be careful! Primary keys are one of the first steps towards creating a normalized database.  Check back soon for a post on normalization!