---
title: Python's If __name__ == "__main__"
tags:
  - boardgames
  - Python
url: 1710.html
id: 1710
categories:
  - Methodology
date: 2016-09-16 08:00:14
---

For the last few months, I've been grabbing data on every game on [Board Game Geek](http://www.boardgamegeek.com).  The script has changed a little bit and I thought it would be a good time to follow up on it.  You can get the script in its entirety [here](https://gist.github.com/allisontharp/38466fc801d7a9dca96de3b3e5f3213f). The script will sometimes error out for any number of reasons.  Typically, it will be because the BGG website is not available (or it kicks me off for pinging it too many times).  When this happens, I need to restart the script.  Part of the problem with this, though, is that I only commit changes to the database periodically.  That means that the last several games that I grabbed may not be in the database, so when I restart the script I need to look at the last game added.  Before, I would do this query manually in the database, then update the script and rerun it.  This takes some time and can be a pain.  In order to make it easier, I wanted to be able to simply run something from the command line that would do the search and restart the script (something like main_xml.py restart). To do this, I use the command:

if \_\_name\_\_ == "\_\_main\_\_":

This statement checks to see if the file is the main file being run.  Since the _main_xml.py_ file is the only script we have, it is the main file.  Within that, I check to see if there are any arguments.  To do that, we check the length of _sys.argv_.  If the length of that is 1, that means there are no arguments.  In that case, it is the first time the script is run so we'll just grab all of the IDs for each game already in the database:

if len(sys.argv) == 1:
    c.execute("SELECT bggid FROM games")
    idlist = c.fetchall()
    idlist = \[int(i\[0\]) for i in idlist\] # convert list of tuples to list of INTs

Otherwise, we will look to see if the argument is 'restart'.  If it is, we'll again grab all of the IDs from the database and store it into the _idlist_.  Next, we'll see what the last game was that was added to the database, and start the search from there:

elif str(sys.argv\[1\]) == 'restart':
    c.execute("SELECT bggid FROM games")
    idlist = c.fetchall()
    idlist = \[int(i\[0\]) for i in idlist\] # convert list of tuples to list of INTs
    query = "SELECT max(bggid) FROM collection where date like '{dt}%'".format(dt = date.today().strftime('%Y-%m'))
    c.execute(query)
    maxid = c.fetchone()\[0\]
    idlist = idlist\[idlist.index(maxid)+1:\]

The if \_\_name\_\_ == "\_\_main\_\_" loop can be really helpful for many scripts.  It is the perfect way to allow your script to accept arguments from the command line, which will help you make your scripts more reuseable!   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._