---
title: Generalized Python Board Game Script
tags:
  - boardgameplays
  - Python
url: 482.html
id: 482
categories:
  - Personal Projects
  - Boardgames
  - Play Tracking
date: 2016-01-26 08:00:26
---

I talked in depth [here ](/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/)about a script I wrote in Python that pulls the play data for all of the board games that my boyfriend play together and puts it into a spreadsheet  (and I talk about the actual spreadsheet [here](/tracking-board-game-plays-part-2-results/)).  I thought others might be interested in doing the same for their own play data, so I generalized the script and spreadsheet.  You can download this new, generalized script [here](https://github.com/allisontharp/BGG_plays_to_Sheets). In order to use the script, you'll need:

*   A google account
*   Python 2.7
*   The Python modules gspread, json, oauth2client, and boardgamegeek

I'm going to assume you already have and are familiar with the programming language Python.  If not, the [Python documentation](https://wiki.python.org/moin/BeginnersGuide/Download) is a good place to start.  If you are interested in using this script but are struggling with the Python aspect of it, let me know and I'll do my best to help you get it up and running. Before running the script, you will need to create a worksheet within Google docs (or copy the one I made [here](https://docs.google.com/spreadsheets/d/18fzphShWJb35Z8iCc3pe-PgM0aR82PHj0Uox4Dljnig/edit?usp=sharing)).  In one of the sheets, you will need the first row (the header row) to include the columns:

*   date
*   gamename
*   winners
*   duration
*   comment

The header row shouldn't contain any spaces and should be all lowercase. Next, you'll need to get a .json file with your Google API credentials.  This is described step-by-step [here](/tracking-boardgame-plays-part-1-pulling-board-game-plays-from-bgg-to-google-sheets/) under Connecting to Sheets with Python.  Follow the steps under **Google Developer** and **Share Your Spreadsheet**. Finally, you will need to edit the script to let it know your credentials.  After the imports section, the first several rows will need to be filled in: \[python\] #-- Attention! Be sure to edit these values! --# json\_file = 'API Project-12345abcd.json' # your json file name (name.json) worksheet = "boardgame\_test" # worksheet name spreadsheet = "Sheet1" # spreadsheet name that you want the data to go into bgg\_username = "mad4hatter" # your bgg username username = "Allison" # your name oldest\_game = datetime(2015,12,31,0,0,0) # year, month, day of the oldest game you want to import (only valid for first time running the script) \[/python\]

*   json_file
    *   File name for your .json file (obtained from Google Developer).  This file should be in the same directory as the python script.
*   worksheet
    *   worksheet name (this is the filename of the Google Spreadsheet)
*   spreadsheet
    *   the name of the sheet which contains, at a minimum, the headers: date, gamename, winners, duration, and comment
*   bgg_username
    *   username of the person whose play data you want to pull
*   username
    *   your name as it appears in your play data.

After that, you should be able to run the script.  If you have any issues, let me know in the comments or [contact me](/contact/).