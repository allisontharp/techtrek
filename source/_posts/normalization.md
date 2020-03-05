---
title: Normalization
tags:
  - MySQL
url: 47.html
id: 47
categories:
  - Methodology
date: 2015-11-10 08:00:59
---

**Normalization** is the process of applying a standardized organizational technique to your database and is a goal of database design.  The **normal form** is the resulting structure of the information in the database.  The goals of normalization are:

*   Eliminate redundant data (storing the same data in multiple tables)
*   Ensure data dependencies make sense (only storing related data in a table)

These are good goals to reach for because it will reduce the amount of space the database consumes as well as ensure that the data is logically stored.  The benefits include eliminating data redundancy, improving performance, optimizing queries, faster updates due to a smaller number of columns in one table, as well as index improvement. There are three levels of normalization: **First Normal Form (1NF)** First Normal Form sets a few basic rules for a database with the goal of minimizing the inclusion of duplicate information in the same table.  For 1NF, you should create separate tables for each group of related data and identify each row with a unique column or set of columns (the [primary key](http://www.techtrek.io/index.php/2015/11/06/primary-keys/)!).  In other words, every cell should only have one piece of information and there should be no duplicate or redundant columns. For example, say you are in charge of making a database for next year's Big Board Game Convention.  You want to have a table to track where each exhibitor will be located in the exhibition hall.  You design the table as shown below, with booth_id as the primary key:

**exhibition_hall**

**booth_id**

**exhibitor_id**

**exhibitor**

1

1

ThatGameCompany

2,4

2

CoolGames

3

3

ContosoGames

5

4

AdventureWorksGames

In order to conform to 1NF, the table must not have any redundant columns and every column must have only one piece of information.  In this example, none of our columns are redundant (such as having columns booth\_id1, booth\_id2, booth\_id3, etc) but the second cell under booth\_id has two values!  In order to fix this, we must split the information in that cell into two rows, as shown below:

**exhibition_hall**

**booth_id**

**exhibitor_id**

**exhibitor**

1

1

ThatGameCompany

2

2

CoolGames

3

3

ContosoGames

4

2

CoolGames

5

4

AdventureWorksGames

**Second Normal Form (2NF)** The next level of normalization is called the Second Normal Form.  For this, you must meet all of the requirements for 1NF in addition to removing subsets of data that apply to multiple rows of a table and place them in separate tables. In the example above, each exhibitor has only one id and each exhibitor\_id has only one exhibitor.  Therefore, we do not need both exhibitor\_id and exhibitor in that table.  If we had another table with just that information, we could find the exhibitor's name by just having its id.  To comply with 2NF, we would break the exhibition_hall into two tables:  

**exhibition_hall**

  

**booth_id**

**exhibitor_id**

1

1

2

2

3

3

4

2

5

4

**exhibitors**

  

**exhibitor_id**

**exhibitor**

1

ThatGameCompany

2

CoolGames

3

ContosoGames

4

AdventureWorksGames

You might be thinking, but what if I want to see the exhibitor name with the booth id?  The exhibitor ids probably don't mean anything to you, but the exhibitor names do!  We can create a query to create a table that looks like our original table by using:

    SELECT booth_id, exhibitor_id, exhibitor
     FROM exhibition_hall
     LEFT JOIN exhibitors
     ON exhibition_hall.exhibitor_id = exhibitors.exhibitor_id;

**Third Normal Form (3NF)** The final normalization is the Third Normal Form.  In order to conform to 3NF, your database must comply with 2NF as well as remove any columns that are not dependent upon the primary key.  This only comes up if you have a **composite key**.  A composite key is a primary key that is made up of the combination of multiple columns in the table. This year, the exhibition hall will be organized a little differently.  The booths will be arranged in a grid, where the rows are lettered and the columns are numbered to get the final booth number.  For instance, ThatGameCompany may be in A1 while CoolGames may be in booth B1.  The different rows will be organized to have specific types of booths, such as booths for kids games, roleplaying games, or dice.  You change your exhibition hall table to reflect this and make the primary key a composite key of both the hall and booth number, as shown below:

**exhibition_hall**

  

**hall**

**boothnum**

**hall_genre**

**exhibitor_id**

a

1

kids

1

b

7

roleplaying

2

b

2

roleplaying

3

c

3

dice

2

a

5

kids

4

In this example, we no longer have booth_id.  Instead, the primary key is the composite of hall and boothnum.  This table does not meet 3NF because hall genre does not depend on the _entire_ composite key.  The hall genre depends only on which hall, or row, the booth is in but is not dependent on boothnum.  To fix this, we must again split this up into two tables:

**exhibition_hall**

  

**hall**

**booth_num**

**exhibitor_id**

a

1

1

b

7

2

b

2

3

c

3

2

a

5

4

 

**halls**

  

**hall**

**hall_genre**

a

kids

b

roleplaying

c

dice

And that's it!  Now, all of our tables are normalized!  When I first read up on normalization, I felt overwhelmed.  It seemed like a lot of memorization and a lot of language that I didn't understand.  However, after working more with databases and database design, normalization seems like a natural and common sense way to design tables.  After a little bit of practice, it will become second nature to you!