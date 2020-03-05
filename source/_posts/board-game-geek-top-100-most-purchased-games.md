---
title: Board Game Geek Top 100 Most Purchased Games
tags:
  - SQLite
  - Python
url: 1282.html
id: 1282
categories:
  - Personal Projects
  - Boardgames
  - BoardGameGeek Scraper
date: 2016-05-13 08:00:47
---

Peter Brahan (aka [amyandpeteb](https://boardgamegeek.com/user/amyandpeteb) on BoardGameGeek) has a few monthly BGG Geeklists where he analyses game purchases.  He is currently doing this in Excel and recently ran into an issue where he had too much data for Excel to handle.  He posted on BGG that he would be ending the monthly "Top 100 most purchased games" Geeklist ([here is an example of this list from March 2016](https://boardgamegeek.com/geeklist/207286/top-100-most-purchased-games-march-2016-edition)).  I am fascinated by the lists he produces and when he said he was running into a limitation with Excel, I thought this could be a really great SQL project.  If I am able to collect all of the data every month, this will be my largest database by far.

### Goals

First, I want to mention that I have no intention of 'competing' with Mr. Brahan.  His work is exceptional.  My main goal here is to get practice working with a large dataset. As for the goals of the project itself, they are:

*   Build a SQL database to store board game name, BGG id, BGG rank, and number of copies owned by BGG users over time.
*   Write a script to automatically pull all of this data into the SQL database.
*   Every month, for every game in the BGG database, get the number of copies owned by BGG users.  This will give an indication of how many copies of the game were sold each month.
*   After the database is updated each month, I want to find the top X games that had the largest increase in ownership over the course of the month.

### Selecting The RDBMS and Programming Language

I now have many tools under my belt as far as programming languages and database management systems.  The choice of programming language is easy in this case.  Python is the clear winner because there is already a [BGG API Python library](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiN3KTg18_MAhVIKCYKHSAMDyEQFggdMAA&url=https%3A%2F%2Fgithub.com%2Flcosmin%2Fboardgamegeek&usg=AFQjCNFfi3scvOCvMO-ceC6QPOqkCADhzg&sig2=8vN5FQYwH43KSkpgw9cmiw) which I have used extensively and understand well. Choosing the database system is not so easy.  I would like to get more practice with SQL Server, so that seems like a good choice.  However, I'm interested in getting this project off the ground quickly and I don't have experience using SQL Server with Python.  I would rather get _something_ up and running and then redo it in SQL Server (since I have more unknowns with SQL Server).  Because I think this database will be pretty simple, I have decided to go with SQLite for the RDBMS over MySQL or MongoDB.  I very well may learn later that this was the wrong choice, but it will be a good learning experience.

### Database Design

I bounced several ideas back and forth for what would be the best database design.  The limiting factor for the database will be the enormous number of board games in the BGG database (about 84,000 unique games).  Additionally, every month I will be adding new data for each current entry as well as more board games.  It'll be a huge database from day one and it will only grow.  One way around dealing with such a huge database would be to somehow only add relevant games.  For instance, if a game is from the late 1980's and only has 100 owners, it probably will not be one of the most purchased games in May 2016.  However, I don't want to miss out on new games just because when they are first released they have few owners.  Additionally, if a game is remade, it may still be linked back to the original game (which maybe wouldn't have been picked up by whatever script I write).  For now, I will try to add all of the games to the database and see about making it 'smarter' later.  I think this is a good move because then I will have _something_ for every game in the BGG database. I want to be able to easily sort the games based on the difference in owners over the course of two months.  Initially, it seemed like the best way to handle this was to have each game be its own table.  However, this doesn't seem very SQL-minded.  Additionally, having 84,000 tables sounds like an unnecessary handful.  Also, when it comes time to rank the games to see the top X most purchased, it would be difficult to do that over so many tables. I have decided to have just two tables in this database.  My gut feel is that this might be the wrong way to go but it will be a good learning experience.  The two tables will be **games** and **collection**.  The games table will hold just the BGG Id (which is unique to every game on the website; therefore, it will be my primary key) and the game name.  The collection table will have the columns _bggid_, _date_, _owned_, and _rank_.  The primary key for this column will be a combination of the bggid and the date.  The owned column will be the number of copies currently owned by BGG users and the rank will be the current BGG rank. Having the database set up this way will make it very easy to pull trends out for a certain game.  However, I think it will be pretty difficult to do the monthly differences since all of the data is in one table.  An idea around this could be to have each month be its own table, but this also doesn't feel very SQL-minded to me.  Again, I think figuring out on my own why this is or is not a good database design will be a tremendously valuable experience.

### Python Script

You can get a copy of the Python script from [my Gist](https://gist.github.com/allisontharp/d815a99b2f29db3c3fec01ee4327852d). First, we import the modules we need:

from boardgamegeek import BoardGameGeek, exceptions
from datetime import datetime
import sqlite3, unicodedata

The **boardgamegeek** module is used to interact with the BGG website and the **datetime** module is used to manipulate datetime objects.  The **sqlite3** module is used to connect and interact with a SQLite database, and the **unicodedata** module is used to convert strings to or from unicode. Next, we set up some initial variables and functions and connect to everything:

db_loc = 'boardgamecollection.sqlite' # name of database
startTime = datetime.now() # Used to calculate total time later

def strip_accents(s):
    return ''.join(c for c in unicodedata.normalize('NFD', s)
                  if unicodedata.category(c) != 'Mn')
    


\# Connect to BGG
bgg = BoardGameGeek()


\# Connect to SQLite Database
conn = sqlite3.connect(db_loc)
c = conn.cursor()

Next, I check to see the two tables currently exist in the database.  If they don't, I create them.

\# Check to see if games table exists
c.execute("SELECT name FROM sqlite_master WHERE type = 'table' AND name = 'games'")
if not c.fetchall(): # if the games table doesn't exist, create it
    print "Creating Games Table"
    c.execute("CREATE TABLE games(bggid INTEGER PRIMARY KEY, name VARCHAR(50) NOT NULL)")

\# Check to see if collection table exists
c.execute("SELECT name FROM sqlite_master WHERE type = 'table' AND name = 'collection'")
if not c.fetchall():
    print "Creating Collection Table"
    c.execute("CREATE TABLE collection(bggid INTEGER, date datetime default current_timestamp, owned INTEGER NOT NULL, rank INTEGER NOT NULL, PRIMARY KEY(bggid, date))")

Most of the script happens in the following _while_ loop.  The idea here is that I increment through all of the BGG IDs until we get to the end (at which point, the _bggcheck _variable will turn to **False**).  Every 100 games, I commit the changes to the database (this way if the code crashes, I don't have to redo all of the games, only the most recent ones).  Much to my surprise, not all BGG IDs have a board game linked.  If an ID does not have a board game attached to it, it will throw one of three errors: BoardGameGeekAPIError, BoardGameGeekError, or AttributeError.  I check for each of these and allow the code to move on so that it doesn't crash. If an ID is valid, I strip any accents from the game name, then I check to see if that game has been added to the _games_ table (by checking the ID).  If it hasn't, I add it.  Next, I get the number of users that own the game and the current BGG rank and add those to the _collection_ table.  I check to see if that was a 100th game added and if so, I commit the changes to the database.  I increment the ID and go again.

bggcheck = True
gamenum = 0
while bggcheck:
    try:
        bgggame = bgg.game(game_id=bggid)
        gamename = bgggame.name
        
        gamenum += 1
        try:
            gamename = gamename.translate(None,"'&-:")
        except TypeError:  
            gamename = unicode(gamename)  # game name from BGG, converted to unicode from string
            gamename = strip_accents(gamename) # remove accents
            
        gamename = gamename.encode("ascii","ignore")
        gamename = gamename.replace("'", "")
        # Check to see if the current game is in the games table
        query = "SELECT * FROM games WHERE bggid = {bi}".\
                format(bi= bggid)
        
        c.execute(query)
        allrows = c.fetchall()
        if not allrows: # bggid has not been added to the games table
            print "    Adding " + gamename + " to games table."
            
            c.execute("INSERT INTO games (bggid, name) VALUES ({bi},'{nm}')".\
                    format(bi = bggid, nm = gamename)) 
        
        owned = bgggame.owned
        rank = bgggame.boardgame_rank
        print (gamename + " - " + str(owned))
        c.execute("INSERT INTO collection(bggid, owned) VALUES ({bi}, {ow})".\
                    format(bi = bggid, ow = owned))
        

        if bggid%100 == 0:
            conn.commit()
        
        bggid += 1
    except exceptions.BoardGameGeekAPIError:
        if bggid < 190000:
            bggid+=1
            pass
        else:
            bggcheck = False
    except exceptions.BoardGameGeekError:
        bggid += 1
        pass
    except AttributeError:
        bggid += 1
        pass



conn.commit()
conn.close()

print "Completed!"
print "    Total games: " + gamenum
print "    Total time: " + str(datetime.now()-startTime)

Unfortunately, this code took several days to run.  For next month, I'd like to only add relevant games to the database so that hopefully it won't take multiple days to run through the script.

### Querying The Database

I could hardly wait for the script to run through everything.  While I waited, I created a similar but **much** smaller database so that I could try to figure out the SQL query to see the top X games.  The query is one of the larger ones I've done and it makes a rather large assumption.  In order for this query to work, the data in the database must be chronological.  Below is the data in my fake _collection_ database:

select * from collection;
1|2016-03-01|299|17
2|2016-03-01|2734|29
3|2016-03-01|10|8394
4|2016-03-01|351|239
5|2016-03-01|42|654
1|2016-04-01|3987|27
2|2016-04-01|293|2983
3|2016-04-01|94|3987
4|2016-04-01|928734|234
5|2016-04-01|93|234
1|2016-05-01|24|24
2|2016-05-01|2384|234
3|2016-05-01|48|234
4|2016-05-01|934|745
5|2016-05-01|2839|374

The columns from left to right are: bggid, date entered, number of users owning the game, and the bgg rank.  Notice how this data was entered with all of the March entries first, followed by April, and May.  The SQL query I came up with relies on this.  While this is a pretty big weakness (and something I hope to fix soon), it is a safe assumption for the work I am doing. I want a SQL query which looks at every game in the list and finds the difference in the number of users who own the game from one month to the next.  For example, in our fake database above, I want it to look at bggid 1 and subtract the number of users who owned that game in April (3987) from the number of users who owned it in may (24) for a total of -3963.  I _don't want_ the difference from March to April, just April to May. Below is the query and the results:

select bggid, rowid, date,
(select min(tt.rowid) from collection as tt where tt.rowid > collection.rowid and tt.bggid = collection.bggid) as next_rowid,
owned,
(select owned from collection as t3 where t3.rowid =
    (select min(tt.rowid) from collection as tt where tt.rowid > collection.rowid and tt.bggid = collection.bggid)) as next_owned,
(select owned from collection as t3 where t3.rowid =
    (select min(tt.rowid) from collection as tt where tt.rowid > collection.rowid and tt.bggid = collection.bggid)) - owned as diff
from collection where next_rowid is not null and strftime('%m', date) = strftime('%m', julianday('2016-04-01'));

1|6|2016-04-01|11|3987|24|-3963
2|7|2016-04-01|12|293|2384|2091
3|8|2016-04-01|13|94|48|-46
4|9|2016-04-01|14|928734|934|-927800
5|10|2016-04-01|15|93|2839|2746

The first line is simple, just select the bggid, rowid, and date.  The rowid is simply the row number that the item is in.  This is the rowid for "last month's" game. Next, we want to find the rowid for the "current month's" game.  We do this with the select statement:

(select min(tt.rowid) from collection as tt where tt.rowid > collection.rowid and tt.bggid = collection.bggid) as next_rowid

Here, we are selecting the next closest rowid for the current bggid. The next select statement finds the difference we are interested in.

(select owned from collection as t3 where t3.rowid =
    (select min(tt.rowid) from collection as tt where tt.rowid > collection.rowid and tt.bggid = collection.bggid)) - owned as diff

This statement is similar to the previous one, only we are now using that rowid to select the _owned_ value and subtracting it from the current _owned_ value. Finally, we tell it the table we are selecting these from (_collection_), and we add two conditionals: we only want to see the values where the next_rowid isn't null (in otherwords, where the next rowid exists) and we only care about the values for a specific month.

from collection where next_rowid is not null and strftime('%m', date) = strftime('%m', julianday('2016-04-01'));

### Results

The code took several days to finish.  In fact, the script will be just over halfway finished when this post is published.  I couldn't wait to share some of the interesting results, so I made a copy of the database so that I could review some of the numbers.  At the time of this writing, **49,830** games had been added to my database.  Shockingly, over half of those games (**25,997**) have 10 or fewer owners! What game do you think is the most owned on BGG?  Here are the top 10 from the 49,830 in my database (which is just over half of all of the games on BGG): Catan|80531 Carcassonne|77305 Pandemic|70014 Dominion|61564 7 Wonders|52687 Agricola|51315 Ticket to Ride|49303 Puerto Rico|47922 Small World|45339 Citadels|43440 In order to get that list, I used the query:

select games.name, collection.owned from games join collection on games.bggid = collection.bggid order by owned desc limit 10;

I can't wait for this script to finish, to make it more efficient, and to work with the data in the coming months.  This is a project that I'm very excited about and I'm interested to see how it will turn out! _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._