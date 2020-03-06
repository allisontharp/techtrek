---
title: 'Inventorying Board Games With A USB Scanner -- Part 1, Overview'
tags:
#   - boardgames
  - Python
url: 1511.html
id: 1511
categories:
  - Personal Projects
  - Boardgames
date: 2016-07-08 08:00:15
---

I want to write a piece of software that will allow me to scan an existing bar code on a board game in order to know what the board game is.  One simple reason why I'd like a piece of software like this is so that I can easily inventory the games in my collection (we have over 500 games).  We have a pretty good idea of each of the games in the collection, but it would be great to scan all of them and know for sure which games we do (or do not!) have. Additionally, I organize an annual board game themed fundraiser called [Play It Forward](http://www.playitforwardfundraiser.com).  The fundraiser is an all day board game event where people come and play board games.  I provide a couple hundred games from my personal collection for people to play.  In the past, we've run simply off the honor system.  We haven't had any issues with this yet, and I doubt we will any time soon, but I feel it would be more professional and safer if we had a check out system in place. The idea here is that each participant will have an ID badge with a unique bar code.  When someone wants to check out a game, they bring the game and their badge to the 'librarian', who then scans both to check the game out (or back in).  I am ridiculously excited for this because it means I will have data on the games being played at my event (which game got checked out the most times?  Which one was checked out for the longest or shortest amount of time? Who checked out the most games? etc).  I also committed myself to trying to write a software like this for a much larger local board game convention in town.  I think it would be a really great experience to write software for another group of people (though I have some of this experience with my [Gun Locker](/gunsmith-db-and-app-part-1-overview/) software).

Overview
--------

A USB scanner connects to your computer as a keyboard input.  This means that when it scans a bar code, it 'types' the UPC as a string into whatever software is open.  [UPCitemdb](http://www.upcitemdb.com/) seems like a reliable web interface for looking up the UPC and receiving the game name.  It also has a [free json based API](https://devs.upcitemdb.com/), so integrating it should be pretty easy.  However, the API limits you to only 100 hits per day. When we scan the games in for the first time, we will store information (including the UPC) in our own database.  This means that after we catalog each of our games, we will no longer need to rely on the UPCitemdb.  This is ideal because we don't want our check out system to rely on internet access or a limit of requests per day.  Therefore, the inventory script is a prerequisite for the check out system. My plan for designing these projects will be as follows: **1\. Build Inventory System**

*   Design and build SQL database for cataloging our collection
*   Design and build script to scan all games in our collection and add them to the SQL database
    1.  Scan bar code
    2.  Look up UPC on UPCitemdb for game name
    3.  Look up game name on [BoardGameGeek](http://www.boardgamegeek.com)
        *   If multiple games with that name exist, notify user and have them select the correct game
    4.  Store at a minimum into the SQL database:
        1.  Game name
        2.  BGGID
        3.  UPC
        4.  Number of copies we own

**2\. Build The Checkout System**

*   Design and build SQL database
    *   Do not duplicate inventory database.  Read from that database only.
*   Design and build script to check games out:
    *   Scan unique player bar code
    *   Scan game bar code
    *   Automatically mark game as checked out and store at a minimum into the SQL database:
        *   Player bar code
        *   Check out time
        *   Game ID
*   Add ability to check games in:
    *   Scan game bar code
    *   Automatically mark game as checked in and store at a minimum into the SQL database:
        *   Check in time

  I am ridiculously excited about this project.  I always have fun designing software that has a hardware component and this project seems very doable.  I'm also excited because it has implications with my fundraiser, which always gets me really pumped.  Last but not least, I think this software could really help our friends with the local convention and I'm really excited to help them out. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._