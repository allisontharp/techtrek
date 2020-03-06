---
title: 'Board Game Play Tracking Using SQLite Part 4: Cleaning Data'
tags:
  - boardgameplays
  - SQLite
url: 900.html
id: 900
categories:
  - Personal Projects
  - Boardgames
  - Play Tracking
date: 2016-03-18 08:18:56
---

My boyfriend and I track all of our board game plays (and we play [a lot](/tracking-board-game-plays-part-2-results/)).  In this series, I'm creating a SQLite database to keep track of all of our data.  If you would like to catch up, I have posted an [overview](/board-game-play-tracking-using-sql-part-1overview/), [details on building the database](/board-game-play-tracking-using-sql-part-2-building-db/), and [populating the database automatically with Python](/board-game-play-tracking-using-sql-part-3-python-automation/).  In this post, we will go over how to clean up data that maybe wasn't initially collected very well.

### The Issue

At some point, I thought it would be a good idea to try to add last names to everyone in the [board game stats app](https://itunes.apple.com/us/app/board-game-stats/id892542000?mt=8) (which pushes the data to my [board game geek profile](https://boardgamegeek.com/plays/bydate/user/mad4hatter/subtype/boardgame)).  This means that at some point, players names on BGG changed and therefore they may be listed under two different names in my database. A similar issue occurred with anonymous players.  Initially, the app did not allow you to add anonymous players.  In order to handle that myself, I added players named 'other', 'other 2', etc.  However, the app eventually added anonymous players (with the names 'Anonymous', 'Anonymous 2', etc).  I would like to merge the 'other's with the 'Anonymous'es.

### The Solution

The plan here is to _delete_ the duplicate name(s) from the Players database and _update_ any references to Players in other tables. First, I need to see all of the people that I think need to be merged.  In order to attempt to quickly see the duplicates, I want to see a list of all of the players in my database and sort them alphabetically by first name: \[sql\] SELECT * FROM players ORDER BY firstname \[/sql\] This allows some of the names to stand out a little easier.  I will wait to delete anything from this table until after everything else is updated -- just in case! Next, I want to make sure I understand all of the tables that need to be updated.  I _think_ I only need to update the 'playersplays' table but I double checked by running: \[text\] .schema tablename \[/text\] for each table in my database.  I could also check the schema I generated using [SchemaCrawler](/using-schemacrawler-to-generate-a-sqlite-db-graph/).  The check confirmed my suspicion.  Once I know the player ids that I want to merge, I can use the command: \[sql\] UPDATE playersplays SET player\_id = (correct player id) WHERE player\_id = (old player id); \[/sql\] I double check to make sure this worked: \[sql\] SELECT * FROM playersplays where player\_id = (old player id); \[/sql\] And then I delete that row in the players table: \[sql\] DELETE FROM players where player\_id = (old player id); \[/sql\] I repeated this several times to make the entries all correct.  After finishing this, I think the data is all correct.  I need to go through Board Game Geek at some point and clean up that data as well in case I ever need to pull it back in.  That will be a project for another day.. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._