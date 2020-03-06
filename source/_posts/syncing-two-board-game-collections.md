---
title: Syncing Two Board Game Collections
url: 330.html
id: 330
categories:
  - Personal Projects
  - Boardgames
date: 2015-12-29 08:00:36
tags:
  - Python
---

As [previously mentioned](/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/), my boyfriend and I play a lot of games.  Together, we also own a few hundred games.  [BoardGameGeek.com](http://www.boardgamegeek.com) (BGG) offers users a way to track what is in their collection, among other things.  Both of us use BGG to keep track of what is in our own collections but we didn't have a way to track what unique games we have together. We had a few reasons for wanting to do this.  First, we were curious how many games we actually own together.  There are a few games we both own and we didn't want those to be counted twice, so having a way to merge the unique games in our collection was appealing.  Additionally, we run an annual fundraiser to raise money for [The Multiple Myeloma Research Foundation](http://www.themmrf.org) called [Play It Forward](http://www.playitforwardfundraiser.com) where players come and play games with us.  We wanted a way for people online to see all of the games in our collection so that they could request specific games for us to bring.  Finally, we are part of a subscription service called [Game Box Monthly](https://www.gameboxmonthly.com/) where we pay a monthly fee and get a game mailed to us to keep.  The service syncs with your BGG account so that you will never get a game you already own.  We wanted to have a BGG account that has all of the games both of us own so that we don't end up getting a game we already have. I was already familiar with [BGG's Python ](https://github.com/lcosmin/boardgamegeek)module from the [script I wrote](/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/) to track my board game plays, so I chose to do this in Python.  The final code is available on [GitHub](https://github.com/allisontharp/BGG_Collection_Sync) and the final output is available [here](https://boardgamegeek.com/collection/user/play_it_forward?subtype=boardgame&ff=1). The first step is to get separate variables for my boyfriend's collection, my collection, and our joint collection.  The code for this is below:  

from boardgamegeek import BoardGameGeek

bgg = BoardGameGeek()

troy = bgg.collection('WhoDislikesKittens')
al = bgg.collection('mad4hatter')
pif = bgg.collection('play\_it\_forward')

Here, _troy_ is my boyfriend's collection, _al _is my collection, and _pif_ is our joint collection (it stands for Play It Forward).  The strings within the .collection attribute are the respective BGG accounts.  A BGG user's collection is not limited to games they currently own -- people can use BGG to track games they used to own as well as games they want.  All of these games will show up in a user's collection.  

to = \[\]
ao = \[\]
po = \[\]

to = \[items.id for items in troy if items.owned\]
po = \[items.id for items in pif if items.owned\]
ao = \[items.id for items in al if items.owned\]

Next, I create empty arrays for each of the collections (Troy is _to_, I am _ao_, and our joint is _po_).  The next three lines use list comprehensions.  In Python, a list comprehension is a way to loop through items and generate an array all within a single line.  The general formula for this is:  

variable = \[something for something\_else in list\_of_things\]

In the actual code, the variable _troy_ has many different items.  Each item corresponds to a game in my boyfriend's BGG collection.  Each item has a unique id (items.id) as well as an attribute (such as items.owned if it is currently owned or items.wishlist if it is something my boyfriend would like to have).  So, in one simple line, Python is able to create a for loop that grabs the unique board game id if it is currently owned by my boyfriend.  List comprehensions are sometimes a little tricky to figure out but they are incredibly powerful. Now that we have a list of all of the games each of us own as well as a list of what is currently in our joint account, I need to check to see if any new games have been added to either my or my boyfriend's collection.  The easiest way to do this is to simply compare what games each of us currently own to the games that are in our joint BGG account.  

tdif = \[i for i in to if not i in po\]
adif = \[i for i in ao if not i in po\]

dif = tdif + adif
dif = list(set(dif))

I again used Python's list comprehension for this.  Each of these list comprehensions (for _tdif_ and _adif_) loop through every game id in our collection.  If a game is not in our joint collection (_po_) then it gets added to the array. Next, I add the two arrays to get one single array of games that one or both of us have purchased but have not yet been added to the joint collection.  Finally, I use the **set** command to get a list of only the unique values in the array.  This way, if my boyfriend and I each bought the same game, it will not be added to our joint collection multiple times. Finally, I need to actually submit the new games to BGG.  

if len(dif) > 0:
for i in range(len(dif)):
add\_game\_by_id(dif\[i\],username,password)

These three lines loop through each item in the final list (dif) and submit them to BGG using the user defined function add\_game\_by\_id.  This user defined function is located in a separate file named add\_game.py.  This file's contents are:  

import requests
def add\_game\_by_id(id,user,passwd):
login_data={'username': user, 'password': passwd, 'B1': 'Submit'}
payload={'objecttype':'thing','objectid':id,'addowned':'true','status':'%5Bobject%20HTMLDivElement%5D','ajax':'1','action':'additem','quickadd':'1'}

with requests.Session() as s:
\# post login data
s.post('https://www.boardgamegeek.com/login', data=login_data,verify=False)
\# Then do another s.post with your collection addition using the JSON payload from above
s.post('https://boardgamegeek.com/geekcollection.php', data=payload,verify=False)
\# Sample Function Call
\# add\_game\_by_id(12750,'WhoDislikesKittens','pass')

First, we import the Python _requests_ module.  Next, we define two different JSON variables: _login_data_ and _payload_.  These variables do two different things, login and post.  The line `with requests.Session() as s:` starts up a session.  This will allow us to login and post the added boardgame to the collection.  The first post line is used to log in to BGG under our joint account and the final post line adds the game, identified by its unique id, to the account's collection. In order to figure out what the payload needed to be, we simply used the web debugger [Fiddler2 ](https://www.telerik.com/download/fiddler/fiddler2)to monitor what happens when you add a game to your collection.