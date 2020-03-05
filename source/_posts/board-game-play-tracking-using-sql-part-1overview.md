---
title: 'Board Game Play Tracking Using SQLite Part 1: Overview'
tags:
  - boardgameplays
  - SQLite
url: 509.html
id: 509
categories:
  - Personal Projects
  - Boardgames
  - Play Tracking
date: 2016-02-12 08:00:36
---

I [currently track](http://www.techtrek.io/tracking-board-game-plays-part-2-results/) my board game plays using Google Docs and a [Python script](http://www.techtrek.io/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/) that I wrote.  When I first started learning MySQL, I wanted to create a database for this data.  However, I saw pretty quickly that it would be too in depth for a first project.  After designing my firearms inventory database, though, I think this one should be pretty easy. Much like my Google Docs tracking, I want to track:

*   Game
    *   Game name
    *   Total hours playing
    *   Average playtime
    *   Average score
    *   Lowest winning
    *   Highest winning
    *   Lowest losing
    *   Highest losing
    *   Times Troy beat me
    *   Times I beat Troy
    *   Troy's total points
    *   Troy's average points
    *   My total points
    *   My average points
    *   Average number of players
    *   Number of locations
    *   Each player's scores
*   Players
    *   Player names
*   Location
    *   Location names
*   Plays
    *   Game Duration
    *   Date Played
    *   Winner
    *   Location
    *   Who wins between my boyfriend and I

  As with all of these projects, this list is living and may very well change before this is over.  Some of it, particularly the 'Game' things I want to track, may just be calculated and won't need to be in the actual database.  My first schema will look like this:   ![boardgameplays](http://www.techtrek.io/wp-content/uploads/2016/02/boardgameplays.png)   Notice this project will have a many-to-many relationship (each player can have many plays and each play can have many players).  I believe this is the first many-to-many relationship that I'll be showing here, except for when I introduced the idea in my post about [relationship types](http://www.techtrek.io/types-of-relationships/). In the next part, we'll start trying to build this database.  I plan on using this project to learn SQLite, so it should be good!