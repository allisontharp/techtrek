---
title: Cleaning Data in SQLite
url: 1635.html
id: 1635
categories:
  - How To
date: 2016-08-09 08:00:05
tags:
---

This past weekend was GenCon.  I updated my database to be able to hold BoardGameGeek Hotness rankings and wrote a script to pull the rankings every hour.  I was hoping to track what was hot during the convention.  I realized after the convention had started that the Hotness rankings only update once every day (sometime around 3 and 4 AM EST).  I was grabbing this data every hour of every day, but it was only changing once.  I wrote a new script to pull data from the GeekBuzz leaderboard (which I'll write about soon!). However, now I have a lot of database entries that are unneeded.  I thought I would take the time to clean this up (even though I'll no longer use the data and could easily just delete the tables).  For the BGG Hotness, I have the tables: hotgame, hotperson, and hotcompany.  I have 7,350 rows in each of those tables, since I collected data on 50 rankings every hour for just over 6 days.  However, since the BGG hotness rankings only update daily, I really only need 300 rows (50 rankings * 6 days = 300 rows). I know think the rankings update between 3 and 4, so I want to only keep the entries from 4:00 AM.  I use the following SELECT statement to make sure I'm in the ballpark with where the data is that I want to keep:

SELECT count(hotid) FROM hotgame WHERE datetime(date,'localtime') like '% 04:%'

The output of this is 300, which is what I expected.  Next, I use the following SELECT statement to make sure I can capture the data I want to delete.

SELECT count(hotid) FROM hotgame WHERE datetime(date,'localtime') like '% 04:%'

The output of this is 7,050, which is what I had hoped and expected.  I feel pretty good about deleting those 7,000 rows, so I go ahead and use the command:

DELETE FROM hotgame WHERE datetime(date,'localtime') NOT LIKE '% 04:%';
select count(hotid) from hotgame;

The output of the SELECT statement was 300, as we would expect.  Finally, as one last sanity check, I use the following command to make sure only the 4 AM entries are now in the database:

SELECT DISTINCT datetime(date,'localtime') FROM hotgame;

Sure enough, the output of that is:

2016-07-31 04:00:01
2016-08-01 04:00:02
2016-08-02 04:00:01
2016-08-03 04:00:02
2016-08-04 04:00:01
2016-08-05 04:00:02

I went ahead and followed the same methodology for the other two tables to clean up the data.  This will make querying a lot easier in the future, should I ever want to look back on the Hotness during GenCon. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._