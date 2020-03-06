---
title: Board Game Geek Database
tags:
  - boardgames
  - SQLite
  - Python
url: 1315.html
id: 1315
categories:
  - Personal Projects
  - Boardgames
  - BoardGameGeek Scraper
  # - Programming
date: 2016-05-24 08:00:52
---

I mentioned a couple of weeks ago that I was interested in pulling data from every board game on BGG and putting it into my own database.  The end goal was to have a database that I could add to every month and see the top 100 games that had the largest increase in ownership over the course of 1 month (or some other time span).  The initial post is [here](http://www.techtrek.io/). This project has turned out to be incredibly fun and fulfilling.  First, I wrote the Python script that I described in that initial post.  This ran through every BGGID on www.boardgamegeek.com (at that time, there were 200k) and checked to see if it was a board game (there were 83k on BGG).  If it was a board game, it first grabbed the name and how many BGG users currently own the game.  This script took _way_ longer to run than I thought and hoped.  In total, it ran for about four days. I could hardly wait for it to finish.  When it was over halfway, I realized that I wanted to also add the current rank of each game to the database.  I decided to let it run instead of starting over (and I'm glad I did!).  In the next few days, I was thinking a lot about the queries I would do when I finally got all of my data.  I realized it would be a lot more interesting if I had more information about each game; specifically, I wanted to also grab all of the publishers and all of the mechanics.  I'm glad I had decided to wait for the script to run through before restarting it! While the script was still running, I made a copy of the database and prepared it for the new data.  The final schema was: ![LqO9U](/wp-content/uploads/2016/05/LqO9U.png) Running through the second time was a little over twice as fast.  This is because I already had a list of the valid BGGIDs for all of the board games.  Instead of looping through 200k objects, I only had to go through about 83k.  This was good, but two days is not acceptable for me if I intend on running this every month.

### Results

I am really excited about generating new queries and learning more about the data and the board game industry.  I have created a Twitter account where I intend on regularly posting interesting tidbits and charts.  If you are interested, follow me [@bggstats](https://twitter.com/bggstats). There was one query I had a particularly hard time figuring out.  After a couple of days struggling with it, I asked on [StackOverflow](http://stackoverflow.com/questions/37336640/select-max-sum-for-each-year-in-sqlite) looking for help.  Pretty immediately afterwards figured out the answer.

#### The Problem

I was interested in getting a list of the most popular mechanics for a particular publisher over time.  In other words, I wanted the result to have the columns date, mechanic, number of copies owned in bgg.  The date column would have one result per year, with mechanic being the most owned (from number of copies). I pretty quickly came up with the query:

select games.year as yr, mechanics.name as mech, sum(collection.owned) as sm
 from games 
 inner join gamemech on games.bggid = gamemech.bggid
 inner join mechanics on gamemech.mechid = mechanics.mechid
 inner join gamepub on gamepub.bggid = games.bggid
 inner join publishers on publishers.pubid = gamepub.pubid
 inner join collection on collection.bggid = games.bggid
 where publishers.name like '%stronghold%'
 group by yr, mech order by yr limit 20;

This returned: \[table width="500" colwidth="20|100|50" colalign="left|left|left"\] year, mechanic, num owned 1974,Commodity Speculation,1460 1974,Dice Rolling,1460 1974,Tile Placement,1460 1982,Action Point Allowance System,16111 1982,Dice Rolling,16111 1982,Modular Board,16111 1982,Secret Unit Deployment,16111 1985,PaperandPencil,1949 1991,Auction/Bidding,1266 1992,Grid Movement,1704 1992,Pickup and Deliver,1704 2011,Action Point Allowance System,7943 2011,Area Control / Area Influence,174 2011,Area Movement,3607 2011,Auction/Bidding,174 2011,Card Drafting,5133 2011,Deck / Pool Building,3768 2011,Dice Rolling,2385 2011,Hand Management,5663 2011,Line Drawing,2141 \[/table\] This is really close.  The data is IN here, but I don't want all of that.  I really only want one result per year - specifically, I want the row that has the max numowned value for that particular year.  I want something that looks more like this: \[table width="500" colwidth="20|100|50" colalign="left|left|left"\] year, mechanic, num owned 1974,Commodity Speculation,1460 1982,Action Point Allowance System,16111 1985,PaperandPencil,1949 1991,Auction/Bidding,1266 1992,Grid Movement,1704 2011,Set Collection,13714 \[/table\] The key here is that I want to select from THAT select.  So, getting that select query was probably the hardest part.  From there, I want to grab the year, mechanic name, and max num owned value.  The trick here is that the initial query (the one showed above) needs to have the columns named.  I named mine yr, mech, and sm.  The final query is:

Select games.year, mechanics.name, max(sm) from (
select games.year as yr, mechanics.name as mech, sum(collection.owned) as sm
 from games 
 inner join gamemech on games.bggid = gamemech.bggid
 inner join mechanics on gamemech.mechid = mechanics.mechid
 inner join gamepub on gamepub.bggid = games.bggid
 inner join publishers on publishers.pubid = gamepub.pubid
 inner join collection on collection.bggid = games.bggid
 where publishers.name like '%stronghold%'
 group by yr, mech)
inner join games on games.year = yr
inner join mechanics on mechanics.name = mech
group by yr;

This returns what I wanted.  I think understanding how this query works and being able to come up with it on my own shows how much I've grown in my database work.  I'm excited for whatever is next.

### Next Steps

There are several things I want to do or at least try to do.  In no particular order, they are:

*   Try to speed the script up.  I have a few things I want to try for this:
    *   Try to use the BGG XML API directly, instead of using the Python module that someone wrote.
    *   Next month (June), run through all 83k games.  After that (beginning in July), have the script look at the ownership values for each game for the last two months.  If there was no change, skip the game (and keep track of the games it skipped).  Do this every month.  Starting the following month (August), do the same thing.  In addition, check the ownership of the first X games that we skipped in July.  In September, do the same for the games that are changing but now do the next X games that we skipped in July.  This will allow the database to keep updating games, but not every game every month.
*   Add more data to the database.  This includes:
    *   Artists
    *   Designers
    *   Category
*   Create a Reddit bot to allow Reddit users to interact with the data.  They could ask it something like "What is the most owned game from 2003" and it could respond with an answer.

Again, if you are interested in the results of this, find me on Twitter [@bggstats](https://twitter.com/bggstats).  If you want to see any charts from the data or want to know any fun facts such as the most owned game from the year you were born, let me know! _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._