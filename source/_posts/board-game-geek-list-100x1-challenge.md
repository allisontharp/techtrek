---
title: Board Game Geek List 100x1 Challenge
tags:
  - boardgameplays
  - Python
url: 1240.html
id: 1240
categories:
  - Personal Projects
  - Boardgames
  - Play Tracking
#   - Python
date: 2016-05-06 08:00:04
---

As previously mentioned, my boyfriend and I play a lot of board games.  I get a lot of enjoyment out of [tracking our plays](/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/) and [analyzing the data](/tracking-board-game-plays-part-2-results/).  Last year, I signed up for a challenge on [BoardGameGeek.com](http://www.boardgamegeek.com) (BGG) titled ["The 2015 open-to-all "100 games x 1" challenge -- Exploring Diversity"](https://boardgamegeek.com/geeklist/183812/item/4304340#item4304340).  The goal of this challenge is to play 100 different games at least one time throughout the year.  I am doing the challenge again this year (you can view my status [here](https://boardgamegeek.com/geeklist/201514/item/4608920#item4608920)).

### The Problem

At the end of each month, I want to generate a list of games that I played for the first time that month.  In addition, I want the list to only include games that I played with my boyfriend.  I want to format it in a way that I can easily copy and paste the output to my BGG post such that it is formatted similar to this: **January** _Stats: 16 plays, 13 unique games (down from 58 plays and 38 unique games in 2015)_ 12 Days of Christmas ![](https://cf.geekdo-static.com/images/star_yellow.gif)BANG! The Duel Betrayal at House on the Hill ![](https://cf.geekdo-static.com/images/star_yellow.gif)Burgle Bros. Dead Drop ![](https://cf.geekdo-static.com/images/star_yellow.gif)Galaxy Defenders Incan Gold Linko! Loony Quest ![](https://cf.geekdo-static.com/images/star_yellow.gif)The Bloody Inn The Bottle Imp ![thumbsup](https://cf.geekdo-static.com/images/thumbs-up.gif)![](https://cf.geekdo-static.com/images/star_yellow.gif)Valeria: Card Kingdoms ![](https://cf.geekdo-static.com/images/star_yellow.gif)Watson & Holmes: From the Diaries of 221B The part in parenthesis (_down from 58 plays and 38 unique games in 2015_) does not need to automatically be generated.  The stars represent games that I played for the very first time.  They are made in BGG by using the text ":star:".  The thumbs up represents the game that we played the most times that month and is made by using the text ":thumbsup:".  It would be nice, though not necessary, to generate hyperlinks for each game that will link to that game's BGG page.

### The Solution

I already have a python script which [pulls my play data and puts it into a Google Sheets spreadsheet](/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/).  The general flow that I think my software needs to follow is:

1.  Ask the user which month and year to analyze
2.  Loop through every game in that month/year (I need two lists: one that contains the names of every game played and the other containing the names of the games that were my first play)
3.  Get a list of every game name played that year
4.  Remove games from the list of games I played this year if that game name is in the list of games that I played this year
5.  Output the list in the format I need

### Working Through The Code

This code is available on my Gist [here](https://gist.github.com/allisontharp/61cebfb59f0c1730db038ea7d5ae5681).

#### Import Modules

The first step is to import our modules.  I don't always know which modules I will need at the start of a project, so often times the import list changes throughout the project.  This was true for this project.  The final import is:  

from boardgamegeek import BoardGameGeek
from collections import Counter
import datetime, unicodedata

The first module is an obvious one.  **BoardGameGeek** is used to get all of our play data.  Next, **Counter** is used to easily figure out which game was played the most in that month.  **Datetime** is used so that we can get only the plays from the specified month and year and **unicodedata** is used to convert the game names to unicode so that we can strip any strange characters (such as letters with accents).

#### Define Functions

Next, we define our function.  This isn't strictly necessary for our project since we only use this function once, but it is a nice function to have for later projects.  

def strip_accents(s):
    return ''.join(c for c in unicodedata.normalize('NFD', s)
                  if unicodedata.category(c) != 'Mn')

This function simply takes an input like Orléans and makes it be Orleans.

#### Define User Inputs

 

\# USER INPUTS
m = 1       # Month number (1 = January, 2 = February, etc)
y = 2016    # Year number (2015, 2016, etc)
bggid = 'mad4hatter'    # BGG username
partnername = 'Troy'        # name of the person you want to track

These are the variables that the user might want to change.  **m** is the month number that we are interested in tracking and **y ** is the year.  **bggid** will be the username of the main person and **partnername** is the name of the person that you want to track your plays with.

#### Initialization

Next, we connect to Board Game Geek and initialize our lists.  

\# connect to BGG
bgg = BoardGameGeek()
myplays = bgg.plays(bggid).plays


     
mgames = \[\]
ygames = \[\]
newplays = \[\]
gameids = \[\]

The BGG connection works like it did previously.  Below are definitions for the lists:

*   **mgames** \- This is a list of the game names from every play in the defined month.  If a game is played multiple times in one month, it will be in this list multiple times.
*   **ygames** \- List of all of the games played in the entire year up to that month (but not including or after the specified month).
*   **newplays** \- Names of games that I played for the very first time in the defined month.  Note, this is different than simply playing it for the first time this year (which is what the overall list is for).  This is specifically a list of the games that I played for the very first time.
*   **gameids** \- BGG id for each game in the defined month.

#### Loop Through Each Game

The meat of the code is in the following for loop:  

for games in reversed(myplays):

    temp = games.players
    partnertest = \['yes' for players in temp if players.name == partnername\]
    if partnertest == \['yes'\]:        
        if games.date.year == y:
            if games.date.month == m:  
                mgames.append(games.game_name)
                gameids.append(games.game_id)
                
                newplayers = ''
                
                aind = \[item for item in range(len(games.players)) if games.players\[item\].name == 'Allison'\]

                if games.players\[aind\[0\]\].new == '1':
                    newplays.append(games.game_name)
            elif games.date.month < m: ygames.append(games.game_name)

This loop goes through every play that I have logged on BGG.  By default, it begins with the most recent play and works backwards in time.  By using the built in 'reversed' function, I'm able to loop through my plays in chronological order. The first thing I do when checking a game is to see if the partner I'm interested in was involved in this game.  If he or she was in the game, we continue to investigate the play further.  If not, we go on to the next play. Next, we check to make sure the play occurred in the year in question.  If it did, we check to make sure the game occurred during the month in question.  If all of this is true (the partner played the game with me and the play happened during the target month and year), then I add the name of the game to the **mgames** list and the game id to the **gameids** list.  Next, I check to see if the play was new to me.  If it is, I add the game name to the **newplays **list. If the play occurred in the correct year but in a _previous_ month, I don't want that play to be added to my list (it should already be in the list in a previous month!).  If this is the case, I add the game name to the **ygames** list.

#### Clean Up **mgames List**

The mgames list contains the game name for every single play in the month (that the partner was also in).  I want a list of the the unique names because ultimately we only want each game to be listed once.  However, having each game listed multiple times makes it easy to figure out how many times we played it in a given month.

mgames = \[game for game in mgames if game not in ygames\] # get the games that you played for the first time this year
mgames_unique = list(set(mgames)) #make unique
mgames_unique.sort() # make list alphabetical

First, I remove all of the games from the mgames list that had already been played in a previous month.  I do this by using a _list comprehension_.  List comprehensions in Python are extremely powerful -- we can do an entire for loop in just one line.  In this case, I am doing the following for loop:

For game in mgames:
    if game not in ygames:
        return game

Next, I get all of the unique game names and sort them alphabetically.

#### Most Played and Final Cleanup

Finally, we need to figure out the most played games.  This is done using the _Counter_ module (within _collections_).  First, we create a variable called **count** by using the _Counter_ function.  This will give us a dictionary which has the key:value pairs "game name":total plays.  The **freq_list** variable is simply a list of the values from the **count** dictionary (so, the number of times each game was played, organized by game name in alphabetical order).  The next line, which defines **max_cnt**,  gets the maximum number of times any game was played.  **Total** counts up how many times a game was played this number of times (just in case there is a tie!).  Finally, the next two lines get a list of the names of the most played game or games (stored in the **mostplayed** variable). The dictionary **d** is unrelated to the most played games.  This variable is simply a dictionary of "game name":BGG ID and is used later to generate the URL for each game.

#### Print Output

Finally, we print the output.  The first part only gets printed once, so it is pretty easy:

print "\[u\]\[b\]" + datetime.date(1900,m,1).strftime('%B') + "\[/u\]\[/b\]"
print "\[i\]Stats:      Fun Facts: \[/i\]"

After that, we must loop through all of the unique game titles, add the url to their BGG page, indicate if it was a new play for me, and finally indicate if it was a most played game.

for games in mgames_unique:
    out = ""
    url="\[url=https://boardgamegeek.com/boardgame/"
    url = url + str(d\[games\]) + "\]"
    if games in newplays:
        out += ":star:"
    if games in mostplayed:
        out += ":thumbsup:"
        
    print(out + url + strip_accents(unicode(games)) + "\[/url\]")

That's it.  This was a really fun project and I'm glad I took some time to complete it.  There is another BGG list where players post statistics about plays on a monthly basis.  I'd like to write code similar to this to automatically generate a post for that as well. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._