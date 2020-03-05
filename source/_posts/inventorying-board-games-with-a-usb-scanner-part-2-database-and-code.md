---
title: 'Inventorying Board Games With A USB Scanner — Part 2, Database and Code'
tags:
  - Python
url: 1515.html
id: 1515
categories:
  - Personal Projects
  - Boardgames
date: 2016-07-12 08:00:24
---

In this part, we will design the database for inventorying a board game collection.  The ultimate goal of this database is to use a USB bar code scanner to enter games into a local SQL database.

### The Schema

This database will be very simple.  We will have just one table: _inventory_.  This table will hold the bggid (the primary key for the table), the name of the game, the UPC, the number of copies we own, and the date it was inventoried. ![boardgameinventory (1)](http://www.techtrek.io/wp-content/uploads/2016/07/boardgameinventory-1.png)   If you are new to MySQL, you can see how to build this database in this [tutorial](http://www.techtrek.io/building-your-first-database/).  The command I used to create this was:

CREATE TABLE inventory (bggid INT NOT NULL PRIMARY KEY,  
upc CHAR(20), owned INT NOT NULL, 
date TIMESTAMP DEFAULT CURRENT_TIMESTAMP);

This table/database is enough to get us through this entire project.  However, I know that I will want more information in the database eventually so I went ahead and converted my BGG database from SQLite to MySQL (I have a tutorial on that [here](http://www.techtrek.io/migrate-from-sqlite3-to-mysql/)).  My BGG database is quite large, so this conversion took many days to complete.

### The Code

The most recent version of the code can be found on my [Gist](https://gist.github.com/allisontharp/e230d56895b0eeed7a3912a281ca9169).  In general, the flow of the script is as follows:

1.  Connect to the MySQL database
2.  Prompt user to enter the UPC
3.  Strip any leading zeros from the UPC
4.  Check to see if the UPC already exists in the database.
    1.  If it does, ask if the user wants to add an additional copy of that game to the database
5.  If the UPC is not in the database, search UPCIndex to try to figure out what the game is.
    1.  If it can find the game, add the game to the database
6.  If it didn't find the game, search UPCitemdb.
    1.  If it can find the game, add the game to the database
7.  If it can't find the game, start altering the names that it grabbed from the UPC websites (remove words like 'board game') and try to find the game.
    1.  If it can find the game, add the game to the database
8.  If it still can't find the game, prompt the user to enter the game name.
    1.  For the user search, it firsts searches exactly what the user types.  If it can't find anything, it starts adding wildcards to the beginning and the end of the string that the user typed.
    2.  If it finds multiple games with the same name, it lists all of the games and asks the user to select the game they want.  The user also has the option to select 'More Info'.  In this case, it will list out all of the publishers for each of the possible game names.

Once a game has been added to the database, it always goes back to step 2.  If a user accidentally adds the wrong game, they can type 'undo' during the UPC prompt to remove the last game from the database.  I've made it so that the user can only undo the previous game.  I don't want someone to be able to come in and undo the entire database.

### Future Development

This is one of those projects that could continually grow and add functionality.  It is working now and in my opinion doesn't _need_ anything right now to be functional.  However, I am trying to design this software with other people in mind, since we may end up giving it to a local convention to use.  With that, below is a list of further development I'd like to complete (in no particular order):

*   After connecting to the database, make sure the inventory table exists.  If it doesn't, create it.
*   Check to see if the BGG database exists.  If it doesn't, don't rely on that database to search for games and instead search BGG directly.
*   Add a 'help' function.
*   Add the ability to somehow read and/or manipulate the database (I'm still thinking about if this is a good idea or not).
*   Allow the database to handle multiple UPC for a single game.
*   When listing games for the user search, add an 'exit' after 'more info'.

  This project is winding up pretty quickly.  I believe the check in/check out system should be really easy to design (both the front and the back end).  I'm really excited to see how this script has progressed and it has been a lot of fun.  It feels really good scanning a game in and seeing the script find it on its own and add it to the database.   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._