---
title: 'Board Game Play Tracking Using SQLite Part 3: Python Automation'
tags:
  - boardgameplays
  - SQLite
  - Python
url: 833.html
id: 833
categories:
  - Personal Projects
  - Boardgames
  - Play Tracking
date: 2016-03-01 08:00:59
---

I am creating a database to track my board game plays.  The first and second posts discuss an [overview](http://www.techtrek.io/board-game-play-tracking-using-sql-part-1overview/) and details on [building the database](http://www.techtrek.io/board-game-play-tracking-using-sql-part-2-building-db/), respectively.  In this part, I'll discuss the Python script I wrote to pull all of my past plays into the database.  The script can easily be modified to create and populate your own database of play data.  If you are interested, you can find the script in its entirety on my [Gist](https://gist.github.com/allisontharp/46111f2a398a734a82a6). This post will be a little different from my other [Python posts](http://www.techtrek.io/category/python/).  Instead of going over the entire script, I want to discuss the struggles I had and the notable things I learned.

Overview
--------

Below is a flow chart of the general things going on in this Python script. ![bgplays_python_automation](http://www.techtrek.io/wp-content/uploads/2016/03/bgplays_python_automation.png) In general, the beginning of the script is typical initialization: imports, defining variables, initializing variables, etc.  Next, it checks to see if the database exists with the correct tables.  If it doesn't it creates the necessary tables. The bulk of the work is done in a large _for loop_.  This loop goes through each of my logged plays and checks to see if the current play is within the specified date range.  If it is, it checks to make sure the game, all of its players, and the play are added to the corresponding tables in the database.  If any of those things are missing, it adds them to the correct table.  Once everything has been submitted to the database, it loops to the next game in the list.

Struggles
---------

These struggles are in no particular order.

#### Special Characters and Unicode Text

This is probably the thing I struggled with the most.  I admittedly need to learn more about some of the major players in the encoding standards so that in the future I can predict and prevent errors like this from occurring. **Symptoms** When trying to post certain game and player names to the database, I received a TypeError stating some of the characters were not recognized. **Issue** After a little bit of digging (and a lot of Python's "print" function), I realized that there were two general issues that were occurring:

1.  Name contained unrecognized characters (such as "é")
2.  Name contained the characters which affected the query statement, such as the character ' which ended the query statement earlier than anticipated

**Solution** The solution was to clean the database text inputs before attempting to submit them to the database.  I do this in a few places throughout the script.  The general method is always the same: \[python\] bgg\_name = games.game\_name try: bgg\_name = bgg\_name.translate(None,"'&-:") except TypeError: bgg\_name = unicode(bgg\_name) # game name from BGG, converted to unicode from string bgg\_name = strip\_accents(bgg\_name) # remove accents bgg\_name = bgg_name.encode("ascii","ignore") \[/python\] Here, I try to remove the characters ', &, -, and :.  If that does not work, I convert the string to unicode and attempt to remove accents and other incorrect characters.

#### Multiple Anonymous Players in One Game

**Symptom** When attempting to add a play with more than one Anonymous Player, I received an error from SQLite which stated the play/player combination was not unique. **Issue** I designed the _playersplays_ table to have a primary key which states that each play/player combination has to be unique.  This is because a single person cannot physically play in the same play of the same game more than one time.  Sometimes, though, I don't want to track a player's name.  This might be because I don't know their name or because I think I won't play many games with them in the future.  In this case, they get logged to BoardGameGeek as 'Anonymous Player'.  If there are more than one Anonymous Players in the same play of a game, the database thinks it is the same person and won't allow them to be added. **Solution** The solution was to track how many Anonymous Players are in each game.  The first Anonymous Player gets added to the database as a player with the first name 'Anonymous' and the last name ''.  Each consecutive Anonymous Player gets a number for their last name (for instance, the 2nd Anonymous Player in a play has the first name 'Anonymous' and the last name '2').

#### Play Group Player Names Incorrect

**Symptom** People I play games with got added to the database with the first name 'Interactions' and the last name '- _TheirCorrectFirstName' _(for example, '- Allison') **Issue** In order to make player tracking a little easier within the app, I added people in my weekly play group as 'Interactions - TheirName'.  This way, I could sort the list of all players alphabetically, and easily find people I play with regularly.  However, this script assumes anything before the first space in a player name is the _first name_ and anything after the first space is the _last name_.  Clearly, this isn't always the case! **Solution** When checking player names, I check to see if 'Interactions' is the first name.  If it is, I make the first name be the name after the '- ' and the last name empty (I have never tracked my weekly game group's last names).

#### Incorrect Number of Plays Added

**Symptom** The output from the script says that 732 plays are added when I try to add all of my plays but BGG says I have 853 logged plays. **Issue** BGG lets you list your plays by [most played game](https://boardgamegeek.com/plays/bygame/user/mad4hatter/subtype/boardgame).  I wanted to recreate this list so I could compare and try to find where my missing 121 games are.  The SQL query for this is: \[sql\] SELECT games.name, count(plays.game\_id) FROM plays JOIN games ON plays.game\_id = games.game\_id GROUP BY plays.game\_id ORDER BY count(plays.game_id); \[/sql\] Right away, I noticed that my database has 13 plays of the game Fairy Tale, while BGG shows 20.  I checked out my plays for this game on BGG and saw that my first log of the game says we played it 8 times.  BGG counts each of these plays individually but groups them into one play in their database. I think this is probably what is causing the difference in play counts, but I checked a few other games to confirm.  Sure enough, this seems to be the issue. **Solution ** I _could_ check for this issue and add new plays.  However, I no longer add plays to BGG in this way so besides the 121 plays that were added this way, no more should be added.  I didn't track duration, scores, or anything else for these 121 plays so I'm not very disappointed to be missing them.  The solution here is to leave this one be.

Next Steps
----------

Again, in no particular order, the next few things I plan to do with this project are:

*   Redo the script to work with MySQL and develop and run tests to see which is faster
*   Find and/or develop tools to visualize the data

That's about it!  I'm really happy with the way this project turned out and the fact that I was able to write this script in less than a weekend.  If you are interested in viewing or using this script, you can grab it on my Gist [here](https://gist.github.com/allisontharp/46111f2a398a734a82a6).  As always, I'd love to hear your suggestions or questions.  Feel free to comment them below or [contact me](/contact/).