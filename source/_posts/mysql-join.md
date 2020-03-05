---
title: MySQL JOIN
tags:
  - MySQL
url: 257.html
id: 257
categories:
  - Methodology
date: 2015-12-25 08:00:37
---

JOIN is a SQL command to combine multiple columns from multiple tables into a single output.  I showed an example of a LEFT JOIN on the post about visualizing my MySQL data from Halloween 2015 [here](http://www.techtrek.io/index.php/2015/12/04/halloween-2015-trick-or-treaters-part-3-visualizing-the-results/). There are four main types of SQL JOINS: LEFT, RIGHT, INNER, FULL OUTER.  If you read up on them, you will very likely come across people attempting to explain the joins using Venn Diagrams, as shown below: ![visual explanation of joins](http://i.stack.imgur.com/1UKp7.png) I find that most of these make sense but a couple (namely, the ones on the top left and right) don't quite make sense to me (why isn't the one on the top right simply a SELECT * FROM TableA?).  In order to explain how these work, I think it would be best to use examples.  I'll use the same data and tables from my [Halloween Trick or Treaters 2015](http://www.techtrek.io/index.php/2015/11/05/halloween-2015-trick-or-treaters-part-1-mysql-database/) database.  Specifically, we will be looking at the _group_ and _adult_ tables. A quick review of the Halloween database:  I wanted to track the costumes and candy preferences of my trick or treaters.  Each group that came to my house was entered as a unique group in the _group_ table.  The _group_ table has the columns: group\_id (the primary key), no\_adults (number of adults in the group), no_kids (number of kids in the group), and gtime (time the group arrived).  Even though GROUP is a MySQL command, it still let me name the table _group_.  I don't recommend doing this! The _adult_ table tracks the adults in each group (if there were any).  It has the columns adult\_id (the primary key), group\_id (the foreign key to the _group_ table), and acostume_id (foreign key to the costume name). The group table has the data: \[sql\]SELECT * FROM halloween.group;\[/sql\]

    
    +----------+-----------+---------+---------------------+
    | group_id | no_adults | no_kids | gtime               |
    +----------+-----------+---------+---------------------+
    | 22       | 0         | 2       | 2015-10-31 17:06:34 |
    | 23       | 0         | 1       | 2015-10-31 17:16:39 |
    | 24       | 0         | 2       | 2015-10-31 17:24:51 |
    | 25       | 2         | 1       | 2015-10-31 17:25:42 |
    | 26       | 1         | 1       | 2015-10-31 17:51:48 |
    | 27       | 3         | 3       | 2015-10-31 17:53:31 |
    | 28       | 2         | 1       | 2015-10-31 18:08:56 |
    | 29       | 0         | 5       | 2015-10-31 18:14:27 |
    | 30       | 2         | 1       | 2015-10-31 18:27:39 |
    | 31       | 4         | 4       | 2015-10-31 18:33:36 |
    | 32       | 0         | 5       | 2015-10-31 18:35:37 |
    | 33       | 3         | 2       | 2015-10-31 18:37:08 |
    | 34       | 2         | 1       | 2015-10-31 18:38:08 |
    | 35       | 2         | 5       | 2015-10-31 18:59:14 |
    | 36       | 3         | 13      | 2015-10-31 19:13:40 |
    | 37       | 0         | 1       | 2015-10-31 19:14:03 |
    | 38       | 0         | 5       | 2015-10-31 19:17:28 |
    | 39       | 0         | 5       | 2015-10-31 19:24:54 |
    +----------+-----------+---------+---------------------+
    18 rows in set (0.00 sec)

The _adult_ table has the data: \[sql\]SELECT * FROM adult;\[/sql\]

    
    +----------+----------+-------------+
    | adult_id | group_id | acostume_id |
    +----------+----------+-------------+
    |       18 |       25 |           8 |
    |       19 |       25 |           7 |
    |       20 |       26 |           9 |
    |       21 |       27 |           1 |
    |       22 |       27 |           1 |
    |       23 |       27 |           1 |
    |       24 |       28 |           1 |
    |       25 |       28 |           1 |
    |       26 |       30 |           1 |
    |       27 |       30 |           1 |
    |       28 |       31 |           1 |
    |       29 |       31 |           1 |
    |       30 |       31 |           1 |
    |       31 |       31 |           1 |
    |       32 |       33 |           1 |
    |       33 |       33 |           1 |
    |       34 |       33 |           1 |
    |       35 |       34 |           1 |
    |       36 |       34 |           1 |
    |       37 |       35 |           1 |
    |       38 |       35 |           1 |
    |       39 |       36 |           1 |
    |       40 |       36 |           1 |
    |       41 |       36 |           1 |
    +----------+----------+-------------+
    24 rows in set (0.00 sec)

JOIN commands will give an output of columns from different tables.  The general format for a JOIN command is: \[sql\]SELECT \[column1\], \[column2\], ... \[columnX\] FROM \[column1 table\] JOIN \[column2 table\], ... \[columnX table\] ON \[condition\]\[/sql\] The different types of JOIN (INNER, LEFT, RIGHT, and sometimes FULL) all work the same way.  _Their only difference is the condition for which they will give rows that have a value of NULL_.

### LEFT JOIN

The Venn diagrams above did not make sense to me until I understood the statement above.  For instance, the LEFT JOIN example seems like a SELECT * FROM tableA statement to me. A LEFT JOIN will result in a table that has the columns listed after SELECT.  It will contain all of the rows of the first column listed.  If no data exists in other columns for a particular row, a result of 'NULL' will be added.

###### _EXAMPLE_

I want to get a table of all of the groups that came to my house as well as the primary key for the adult that was with the group.  The two tables this information will be in are _group_ and _adult_ and the rows are group_id from _group_ and adult_idfrom _adult_.  Since I want all of the groups to be listed, the condition will be when the group_id from the _group_ table equals the group_id from the _adult_ table.  I have found that the condition will generally be where the primary key of one table equals the foreign key of the other (which is the case here: adult.group_id is the foreign key in the _adult_ table that points back to the _group_ table). \[SQL\]SELECT halloween.group.group\_id, adult\_id FROM halloween.group LEFT JOIN adult ON halloween.group.group\_id = adult.group\_id;\[/SQL\] The result is:

    
    +----------+----------+
    | group_id | adult_id |
    +----------+----------+
    |       22 |     NULL |
    |       23 |     NULL |
    |       24 |     NULL |
    |       25 |       18 |
    |       25 |       19 |
    |       26 |       20 |
    |       27 |       21 |
    |       27 |       22 |
    |       27 |       23 |
    |       28 |       24 |
    |       28 |       25 |
    |       29 |     NULL |
    |       30 |       26 |
    |       30 |       27 |
    |       31 |       28 |
    |       31 |       29 |
    |       31 |       30 |
    |       31 |       31 |
    |       32 |     NULL |
    |       33 |       32 |
    |       33 |       33 |
    |       33 |       34 |
    |       34 |       35 |
    |       34 |       36 |
    |       35 |       37 |
    |       35 |       38 |
    |       36 |       39 |
    |       36 |       40 |
    |       36 |       41 |
    |       37 |     NULL |
    |       38 |     NULL |
    |       39 |     NULL |
    +----------+----------+
    32 rows in set (0.00 sec)
    
    

Notice the 'NULL' values in several of the rows.  This indicates that there were no adults in those groups!

### RIGHT JOIN

RIGHT JOIN is similar to LEFT JOIN.  However, it will only list rows that have values in the second column.

###### _**EXAMPLE**_

\[sql\]SELECT halloween.group.group\_id, adult\_id FROM halloween.group RIGHT JOIN adult ON halloween.group.group\_id = adult.group\_id;\[/sql\] The result is:

    
    +----------+----------+
    | group_id | adult_id |
    +----------+----------+
    |       25 |       18 |
    |       25 |       19 |
    |       26 |       20 |
    |       27 |       21 |
    |       27 |       22 |
    |       27 |       23 |
    |       28 |       24 |
    |       28 |       25 |
    |       30 |       26 |
    |       30 |       27 |
    |       31 |       28 |
    |       31 |       29 |
    |       31 |       30 |
    |       31 |       31 |
    |       33 |       32 |
    |       33 |       33 |
    |       33 |       34 |
    |       34 |       35 |
    |       34 |       36 |
    |       35 |       37 |
    |       35 |       38 |
    |       36 |       39 |
    |       36 |       40 |
    |       36 |       41 |
    +----------+----------+
    24 rows in set (0.00 sec)
    

Every adult had to come in a group, so there are no NULL values in this output.

### INNER JOIN

INNER JOIN will only give an output if there are values in all of the columns.  You won't get 'NULL' values using an INNER JOIN.

###### _**EXAMPLE**_

\[SQL\]SELECT halloween.group.group\_id, adult\_id FROM halloween.group INNER JOIN adult ON halloween.group.group\_id = adult.group\_id;\[/SQL\] The result is:

    
    +----------+----------+
    | group_id | adult_id |
    +----------+----------+
    |       25 |       18 |
    |       25 |       19 |
    |       26 |       20 |
    |       27 |       21 |
    |       27 |       22 |
    |       27 |       23 |
    |       28 |       24 |
    |       28 |       25 |
    |       30 |       26 |
    |       30 |       27 |
    |       31 |       28 |
    |       31 |       29 |
    |       31 |       30 |
    |       31 |       31 |
    |       33 |       32 |
    |       33 |       33 |
    |       33 |       34 |
    |       34 |       35 |
    |       34 |       36 |
    |       35 |       37 |
    |       35 |       38 |
    |       36 |       39 |
    |       36 |       40 |
    |       36 |       41 |
    +----------+----------+
    24 rows in set (0.00 sec)
    

### FULL OUTER

MySQL does not support FULL OUTER JOIN, though other versions of SQL do.  In order to emulate a FULL OUTER JOIN in MySQL, you would combine a LEFT JOIN and a RIGHT JOIN.  A FULL OUTER JOIN would give all of the rows from both tables, even those that have a value of 'NULL'.   JOIN is a really powerful MySQL command because it allows you to compare columns from different tables.  The key to choosing which type of JOIN command you want to use is to decide what 'NULL' data you want or need.