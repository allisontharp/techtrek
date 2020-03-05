---
title: 'NFLdb Part 1: Creating an NFL Stats Database'
tags:
  - nfldb
  - Python
url: 193.html
id: 193
categories:
  - Personal Projects
  - NFLdb
date: 2015-11-27 08:00:48
---

In the spirit of Thanksgiving, I thought I would start making an NFL stats database.  I have been getting more interested in professional football with each passing season and the amount of data that is collected from every game is enormous (and is reportedly growing.  [The NFL has been playing with RFID chips in player's uniforms to track all kinds of information!](http://www.wired.com/2015/08/nfl-players-getting-rfid-chips-season/)).  This will be my largest database to date by far.  Additionally, until now all of my posts have been about things I've already done.  This series will be in 'real time'.  I will track my thought process, what I've learned, and the specifics of how I'm making the database.

### Overview

##### Programming Language

I know from the beginning that I don't want to have to type any of these stats in by hand, so it without a doubt needs to be done programmatically.  Since I am fairly familiar with Python, this is an easy choice.

##### Database Design

I first started thinking about what the tables should be, how they are related, what data will be in them, etc.  This is a mistake.  When doing a large project such as this, we need to first think _what data do I want_ and _what will I do with it?  _In order to answer the first, it may be easier to answer the second: **What will I do with the data I collect?** My primary goal of this is to make my first, large-scale database and to learn the ins and outs of what that really means.  After that, I think it would be fun to be able to come up with the types of stats you hear the announcers say during games such as _When the Colts are away and up by X points at the half, they have won A/B games in the last Y years._  I am genuinely excited to get all of this data and to come up with those silly stats. **What data do I want?** I would like to eventually collect as much data as possible, but in order to keep this project realistic I need to prioritize and do it in chunks.  I want to start with basic stats.  Football stats are typically separated into these categories (this is not all inclusive but seems like a good start):

*   Passing
*   Rushing
*   Receiving
*   Fumbles
*   Kicking
*   Punting
*   Kick Returns
*   Punt Returns
*   Defense
*   Penalty

I suspect it will also be easy to get static data about players such as:

*   Full name
*   Height
*   Weight
*   Position
*   Team
*   College

It should also be pretty easy to get data on teams:

*   Team Name
*   Stadium
*   Same stats as above for full seasons (total passing, total rushing, etc)

I am using this as a general starting point or template.  I'm not holding myself to all of this information; rather, I'm using it as a goal to reach for.  

### Python

I want to see what data I can realistically get with Python before I start to design the databases (this way I know exactly what data is going into the database instead of just guessing).  After a little bit of searching, I found a Python module called [nflgame](https://github.com/BurntSushi/nflgame). This module has better than average [documentation](http://pdoc.burntsushi.net/nflgame) but I still found myself asking lots of questions.  I would typically skip writing about the steps of figuring things like this out but I want to try to be as thorough as possible for this series. To get this module, in the command prompt type: `pip install nflgame` The example on the module's website shows how to get the top five running backs by rushing yards for the first week in 2013.   \[python\] import nflgame games = nflgame.games(2013, week=1) players = nflgame.combine\_game\_stats(games) for p in players.rushing().sort('rushing\_yds').limit(5): msg = '%s %d carries for %d yards and %d TDs' print msg % (p, p.rushing\_att, p.rushing\_yds, p.rushing\_tds) \[/python\] From this, I can tell that rushing() is an attribute of nflgame.combine\_game\_stats() and that I can easily get rushing\_att (rushing attempts), rushing\_yds (rushing yards), and rushing\_tds (rushing touchdowns).  Lots of good information here but it brings up many questions.  What are the other attributes of nflgame.combine\_game\_stats besides rushing() and does rushing() have any more than the three listed here? Checking the attributes of combine\_game\_stats is easy within python. \[python\] import nflgame games = nflgame.games(2015, week=9) players = nflgame.combine\_game_stats(games) dir(players) \[/python\] First, we import the module and set the week for games we want to look at (the week doesn't matter, so I chose the most recent games).  Then, we get the players from that week.  In order to see the attribute of players, we simply use the line

    dir(players)

_Dir()_ is a built-in function of Python.  If you give it an argument (in other words, put something in between the parentheses) it will try to return a list of the attributes for that object.  Without an argument, it returns a list of names in the current local scope. Our output that looks like this:

    ['_GenPlayerStats__filter_category', '_Gen__iter', '__add__', '__class__', '__de
    lattr__', '__dict__', '__doc__', '__format__', '__getattribute__', '__hash__', '
    __init__', '__iter__', '__module__', '__new__', '__reduce__', '__reduce_ex__', '
    __repr__', '__reversed__', '__setattr__', '__sizeof__', '__str__', '__subclassho
    ok__', '__weakref__', 'csv', 'defense', 'filter', 'fumbles', 'kicking', 'kickret
    ', 'limit', 'name', 'passing', 'penalty', 'playerid', 'punting', 'puntret', 'rec
    eiving', 'rushing', 'sort', 'touchdowns']

It is readable but just barely so.  In order to make this more manageable, we need to use something called pretty print.  To install it, use

    pip install pprintpp

Then, within python we will do this: \[python\] from pprintpp import pprint as pp pp(dir(players)) \[/python\] The output of pprint is much more manageable:

    [
     '_GenPlayerStats__filter_categ
     '_Gen__iter',
     '__add__',
     '__class__',
     '__delattr__',
     '__dict__',
     '__doc__',
     '__format__',
     '__getattribute__',
     '__hash__',
     '__init__',
     '__iter__',
     '__module__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__reversed__',
     '__setattr__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     '__weakref__',
     'csv',
     'defense',
     'filter',
     'fumbles',
     'kicking',
     'kickret',
     'limit',
     'name',
     'passing',
     'penalty',
     'playerid',
     'punting',
     'puntret',
     'receiving',
     'rushing',
     'sort',
     'touchdowns',
    ]

This is looking good!  We can see the typical attributes such as \_\_init\_\_ at the top.  The ones I am intrigued by, however, are towards the bottom.  I see rushing is in there, which I already knew, but I can also find defense, fumbles, kicking, kickret, passing, etc.  This list looks a LOT like the stats categories that I had already established at the first part of this post. Now, I just need to know all of the attributes of everything in this list.  Unfortunately, dir() does not work here so I started looking at the source code for the module.  After a little bit of digging (I searched the source files for 'rushing_att' since I know this is an attribute and it is something that I didn't expect to be repeated a lot throughout the code), I found what I believed to be the list of all of the stats.  This file is located in _C:\\Python27\\Lib\\site-packages\\nflgame\\statmap.py._ I started with passing stats.  There were close to 10 sections of the statmap file that looked like this, where 'cat' was equal to 'passing': \[python\] 14: { 'cat': 'passing', 'fields': \['passing\_att', 'passing\_incmp'\], 'yds': '', 'desc': 'Pass incomplete', 'long': 'Pass atempt, incomplete.', }, \[/python\] From here, I am making an assumption that passing().passing\_att will give the passing attempts in the same way that rushing().rushing\_att gave rushing attempts.  If this is the case, then passing().passing\_incmp will probably also give the number of incomplete passes. I didn't want to assume this is how the code works, so I began to test it with a simple script.  I first went through and found all of the things that I believed to be attributes of 'passing' from the statmap.py file and put them into a script to print out the numbers of the top 5 passers: \[python\] for p in players.passing().sort('passing\_yds').limit(5): msg = '%s made %d passing attempts with %d incompletions and %d completions.&nbsp; He had a total of %d yards, %d touchdowns, %d interceptions, %d sacks, and lost a total of %d from the sacks' print msg % (p, p.passing\_att, p.passing\_incmp, p.passing\_cmp, p.passing\_yds, p.passing\_tds, p.passing\_int, p.passing\_sk, p.passing\_sk_yds) \[/python\] The output of this is:

    D.Brees made 39 passing attempts with 0 incompletions and 28 completions.  He had a total of 389 yards, 3 touchdowns, 0 interceptions, 0 sacks, and lost a total of 0 from the sacks
     
    B.Bortles made 40 passing attempts with 0 incompletions and 24 completions.  He had a total of 381 yards, 2 touchdowns, 0 interceptions, 0 sacks, and lost a total of 0 from the sacks
     
    M.Mariota made 39 passing attempts with 0 incompletions and 28 completions .  He had a total of 371 yards, 4 touchdowns, 0 interceptions, 0 sacks, and lost a total of 0 from the sacks
     
    A.Rodgers made 48 passing attempts with 0 incompletions and 25 completions.  He had a total of 369 yards, 4 touchdowns, 0 interceptions, 0 sacks, and lost a total of 0 from the sacks
     
    J.Cutler made 40 passing attempts with 0 incompletions and 27 completions.  He had a total of 345 yards, 2 touchdowns, 0 interceptions, 0 sacks, and lost a total of 0 from the sacks

Right off the bat, I noticed a lot of zeros which is unexpected.  Upon closer look, all five quarterbacks have 0 incompletions, interceptions, sacks, and lost 0 yards from sacks in week 9 of the 2015 season.  This seemed too odd to be true but I double checked Drew Brees' week 9 stats just to be sure.  According to [Pro Football Reference](http://www.pro-football-reference.com/players/B/BreeDr00.htm), Brees did indeed have 28 completions and he also had 39 attempts (meaning 19 incompletions).  He had 3 touchdowns for 387 yards (a difference of 2, which I think is reasonable), but he had 1 interception.  Sacks aren't listed in Pro Football Reference, but I think it is a safe assumption that something is up with the incompletions, interceptions, sacks, and sack yardage. I noted these attributes and repeated this process for rushing, receiving, fumbles, kicking, punting, kick returns, punt returns, defense, and penalties.  The table below summarizes the findings.

**Category**

**Attribute**

**Working**

**Passing**

passing_att

passig_incmp

N

passing_cmp

passing_yds

passing_tds

passing_int

N

passing_sk

N

passing\_sk\_yds

N

**Rushing**

rushing_att

rushing_tds

rushing_yds

**Receiving**

receiving_rec

receiving_yds

receiving_tds

N

receiving_tar

N

receiving\_yac\_yds

N

**Fumbles**

fumbles_tot

fumbles_forced

N

fumbles_notforced

N

fumbles_oob

N

fumbles_rec

N

fumbles\_rec\_yds

N

fumbles\_rec\_tds

N

fumbles_lost

**Kicking**

kicking_tot

N

kicking_yds

N

kicking_i20

N

kicking_touchback

N

**Punting**

punting_tot

N

punting_yds

punting_i20

punting_touchback

N

**Kickret**

kickret_ret

kickret_yds

N

kickret_tds

kickret_oob

N

kickret_fair

N

kickret_touchback

N

**Puntret**

Puntret_tot

N

Puntret_tds

N

Puntret_yds

N

Puntret_oob

N

Puntret_downed

N

Puntret_fair

N

Puntret_touchback

N

It is pretty obvious when looking at this list that the majority of the stats are producing 0 when I attempt to print out the top five players for the stat.  I know that this isn't an abandoned project since the latest commit on github was only 19 days ago which makes me have a feeling that these stats should be working. I will continue to troubleshoot and perhaps get on the [creator's IRC](http://webchat.freenode.net/?channels=%23nflgame) and chat with him.