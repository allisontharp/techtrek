---
title: 'Gunsmith Db and App Part 1: Overview'
tags:
  - gunsmith db
  - MySQL
url: 284.html
id: 284
comments: false
categories:
  - Personal Projects
date: 2015-12-18 08:00:05
---

### **Background**

I have a friend who owns his own gunsmith business.  By law, he must track the acquisition and disposition of his guns.  His current method of doing this is by filling out, by hand, papers issued by the government.  Once he has so many entries in his logbook, it gets stored away for a certain number of years before he can purge it.  He has no backup for this data so if something were to happen to the place it is stored, he would be out of luck.  He would also often like to have the ability to search through the guns he has or has had and who the transaction was with.  However, with paper records this is very tedious.  When I heard about this problem, I thought it was the perfect project for where I currently am in learning MySQL Any gun his business has will fall into one of three categories: gunsmithing, retail, or manufactured.  Gunsmithing guns are guns that someone is essentially loaning to him and asking him to do some kind of modification to.  These guns will generally come in and go back out to the same person.  Retail guns are guns he has purchased from another person or entity with the intent to sell to a different person.  Finally, manufactured guns are guns that he has made himself with the intent to sell.  This gunsmith database should handle and track guns from any of these categories.

### **Requirements**

The United States takes gun tracking seriously.  They have defined regulations for keeping electronic records for firearm acquisition and disposition.  An electronic system must comply with these regulations, which means a person from the government much check the system to make sure it meets all of their regulations.  Obviously, my primary goal must be to meet all government requirements.  These are laid out in [ATF Ruling 2013-5](https://www.atf.gov/file/11226/download) but in general the requirements are that within 15 days of acquisition, the _type, model, caliber/gauge, manufacturer, country of manufacture, name and address of other person in the transaction, _and the _serial number_ must be recorded. Additionally, my friend has a few requirements of his own.  He would like the system to be _searchable and sortable, _it should also have the ability to _generate custom reports._  He would also like the ability for the system to _send him an email_ on a defined scheduled that lets him know which guns are late or which orders are still open.  Other things he would like to track are _deadline, category (gunsmithing, retail, manufacturered), purchase price, _and _date entered._ Finally, I have requirements of my own.  Where possible, the GUI needs to use _dropdown menus_ to prevent incorrect data being entered.  In addition, some fields will need to be _required_ before the GUI allows the data to be submitted. I'm sure more requirements will come as the project gets further along, but this should do for a starting point.

### **Schema**

Below is a sketch of what the schema should look like.  Green items are required by the ATF ruling and red are not. ![2015-12-16_22h02_38](/wp-content/uploads/2015/12/2015-12-16_22h02_38.png)

### **GUI**

Below is my rough draft GUI.  It has more text boxes than the final have, but overall I envision the final GUI to look very similar to this one. ![2015-12-16_22h04_50](/wp-content/uploads/2015/12/2015-12-16_22h04_50-1.png) The dropdown menus in this GUI are populated from tables in the database.  In this way, no data is hardcoded into the GUI.

### **Database**

I started working on this project a few months ago but got busy and was unable to finish.  This database is stored on a server, not my local machine.  In order to connect to it, I use [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  The database name is 'firearms'.  In order to see the tables I have already created in the database, I can use: \[SQL\] USE firearms; SHOW tables; \[/SQL\] The result is:

    +--------------------+
    | Tables_in_firearms |
    +--------------------+
    | acquisition        |
    | caliber            |
    | category           |
    | disposition        |
    | gun                |
    | mancountry         |
    | manufacturers      |
    | models             |
    | test               |
    | type               |
    +--------------------+
    10 rows in set (0.00 sec)
    

Since it has been awhile since I worked on this project, I'm not sure if these tables were set up correctly (or even at all!).  In order to check, I use the DESCRIBE command: \[SQL\]DESCRIBE gun;\[/SQL\]

    +---------------+--------------+------+-----+-------------------+-----------------------------+
     | Field          | Type         | Null| Key | Default           | Extra                    |
     +---------------+--------------+------+-----+-------------------+-----------------------------+
     | gun_id         | int(11)      | NO  | PRI | NULL           | auto_increment              |
     | type_id        | int(11)      | NO  |     | NULL           |                             |
     | cal_id         | int(11)      | NO  |     | NULL           |                             |
     | man_id         | int(11)      | NO  |     | NULL           |                             |
     | model_id       | int(11)      | NO  |     | NULL           |                             |
     | mancountry_id  | int(11)      | NO  |     | NULL           |                             |
     | serialnum      | int(11)      | NO  |     | NULL           |                             |
     | date_entered   | timestamp    | NO  |  | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
     | year           | int(11)      | YES |     | NULL           |                             |
     | cat_id         | int(11)      | YES |     | NULL           |                             |
     | deadline       | date         | YES |     | NULL           |                             |
     | isupdated      | tinyint(1)   | NO  |     | NULL           |                             |
     | prev_record    | int(11)      | YES |     | NULL           |                             |
     | record_note    | varchar(250) | YES |     | NULL           |                             |
     | note           | varchar(250) | YES |     | NULL           |                             |
     +---------------+--------------+------+-----+-------------------+-----------------------------+
     15 rows in set (0.00 sec)

This looks as though it is set up correctly, following the schema I had laid out.  I checked each of the tables, and they all seemed to be OK. If I wanted to see if there was any data in any of the tables, I could use the SELECT command: \[SQL\]SELECT * FROM type;\[/SQL\]

    +---------+------------+
    | type_id | name       |
    +---------+------------+
    | 1       | shotgun    |
    | 2       | pistol     |
    | 3       | revolver   |
    | 4       | suppressor |
    | 5       | rifle      |
    +---------+------------+
    5 rows in set (0.00 sec)

I don't particularly care for the data in most of these tables because it is just filler data. Now that I think I'm up to speed with what I've done, I can start working toward finishing the application and database.  Things I need to do still are:

*   Figure out how to securely connect to the DB
*   Set up the DB to backup on a regular basis
*   Start and finish the disposition and search functions on GUI

Once I get each of these three things finished, I can send the application off to my friend.  He won't be able to use it solely, but I would like for him to start using it in conjunction with his current method so that we can both see what works and what doesn't.  While he is using it, I will continue to add features he has requested (such as the reporting). That's it for the overview!  The next several parts in this series will go further in depth on how I designed the system and my thought processes behind different decisions along the way.  Check out [Part 2: Adding Customers](http://www.techtrek.io/gunsmith-db-and-app-part-2-adding-customers/).