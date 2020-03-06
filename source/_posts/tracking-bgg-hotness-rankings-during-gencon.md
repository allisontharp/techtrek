---
title: Tracking BGG Hotness Rankings During GenCon
tags:
  - boardgames
  - Python
  - SQLite
url: 1614.html
id: 1614
categories:
  - Personal Projects
  - Boardgames
date: 2016-08-02 08:00:40
---

[GenCon](http://www.gencon.com/) is this week!  GenCon is the "original, longest-running, best-attended, gaming convention in the world".  I'm lucky enough to have it very close to where I live, so making it out there is no big deal. Since I have a big board game database already (and because I've been slacking on my [@BGGStats](http://www.twitter.com/bggstats) account), I thought it would be a lot of fun to pull some data from [BoardGameGeek](http://www.boardgamegeek.com) (bgg) during GenCon.  I wanted to pull something that might give a hint as to what games are hot at GenCon, what people are looking at, what people are playing, etc.  Luckily, BGG already has a tool similar to this called 'The Hotness'.  On the BGG homepage, The Hotness is off to the left hand side. The goal of The Hotness is to show a dynamic view of what people on BGG are interested in at any given time (broken down to Games, People, and Companies).  To my knowledge, BGG has never released their exact formula for calculating The Hotness, but we know that it has to do the number of views a page gets over an amount of time. The data from The Hotness is available via the BGG XML API (which I discussed [here](/how-to-parse-xml-data-with-python-from-url/)).  Given all of that, I thought this would be the perfect way to monitor what is going on at GenCon.

### The Database

I already have the backbone of the database done (you can read all about that [here](/board-game-geek-database/)).  All I need to do is add tables for the hotness.  I want to track each of the three categories, so I'll go ahead and make three tables (one each for Games, People, and Companies).  They will all be the same, so I'll focus on the Games table. ![Capture](/wp-content/uploads/2016/07/Capture-10.jpg) The table will have an auto incrementing primary key.  The rest of the columns will be _bggid_(foreign key to the games table), _rank_, and _date_ (default to the current timestamp).

### The Code

The code for each category is generally the same.  However, the games will be slightly easier.  Below is the Python script to pull the BGGID and Rank of all 50 games in the hotness:

"""
    Pull hotness data from BGG (wwww.boardgamegeek.com) and put into SQLite Db
"""

from xml.dom import minidom
import sqlite3, urllib2

db\_loc = 'gencon\_test.sqlite'

url = 'https://www.boardgamegeek.com/xmlapi2/hot?type=boardgame'

\# Connect to SQLite Db
conn = sqlite3.connect(db_loc)
c = conn.cursor()

\# TO DO: Check to see if tables exist
\# TO DO: Add companies and people (need to check to get their PK)


dom = minidom.parse(urllib2.urlopen(url))

games = dom.getElementsByTagName('item')

i = 0
for elem in games:
    bggid = int(games\[i\].attributes\['id'\].value)
    rank = int(games\[i\].attributes\['rank'\].value)
    
    query = "INSERT INTO hotness (bggid, rank) VALUES ({bi}, {rk})".format(bi = bggid, rk = rank)
    c.execute(query)
    
    print str(bggid) + ": " + str (rank)
    i += 1
    
    
conn.commit()
conn.close()

This script is very similar to the one that I use every month to pull information on all of the games on BGG.  First, we import all of modules (xml.don, sqlite3, and urllib2).  Then, we let the script know where the database is that we want to add the data to and the URL that we want to pull the data from. Then, we connect to the database.  We only have to do this once.

dom = minidom.parse(urllib2.urlopen(url))

The next line, also shown above, opens the URL and parses the XML data.  With that, we pull out all of the elements that have the tag name 'item'.  This is because the XML data has each of the 50 Hotness games categorized as an 'item'. Finally, I loop through each of those elements and grab the id (which is the BGGID) and the rank.  I run a simple INSERT query to put the data into the database.  To commit the changes to the database, we run the commit and close commands at the end. This was repeated for the people and company categories.  The full code is available on my Gist [here](https://gist.github.com/allisontharp/460cb61fa1a1930eac5c08d255aac686). If there is anything you'd like tracked at GenCon, let me know and I'll try to help you out.  I'll also have some cool Play It Forward badge ribbons if you're interested in getting the word out on [my annual fundraiser](http://www.playitforwardfundraiser.com).   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._