---
title: Updated NFL Alerts Python Script
tags:
  - nfldb
  - Python
url: 1708.html
id: 1708
categories:
  - Personal Projects
  - NFLdb
date: 2016-09-09 08:00:16
---

Earlier this week, I [posted about](http://www.techtrek.io/python-script-fo…-big-play-alerts/) a Python script to send alerts when the Colts or their opponent have a big play in a game.  I will not be able to watch their regular season opener game, so I wanted to broaden the script and schedule it to be run during that day.  This way, I'll still be up to date with the big plays even though I know I won't be able to watch the game. Broadening the script was simple.  I added the following variables to the beginning of the script and replaced the corresponding values with their new variables:

hometeam = 'IND'
awayteam = 'IND'
week = 1
year = 2016
kind='REG'

Down the line, I could completely functionize the script, and allow these variables to be declared within the command prompt when the script is called.  For now, I decided to just leave them in the script itself.  

### Scoring Plays

Next, I wanted to make the alerts be a little more meaningful.  The alert for a scoring play was already pretty good - it sends something like: _BUF - Q4 - TD - J.Boykin 4 yd. pass from C.Jones (pass failed) Drive: 8 plays, 83 yards in 1:08 IND (19) at BUF (18)_.  This is good, and in fact it is what I want the rest of the alerts to look like.  However, I'd like the subject of the email to have the name of the team that scored (before it was just 'Scoring Play').  To do that, I needed to find out how to get the name of the scoring team.  This was a little tricky because the documentation for the nflgame library, though pretty good, doesn't give a good indication on how to find this.  I was able to figure out how to get a list of all of the plays:

games = n.games(year=year, week=week, kind=kind, away=awayteam, home=hometeam)
plays = n.combine_plays(games)

There were several issues with this, though.  For one, you can only call the _plays_ instance once.  I also couldn't figure out how to get the last play.  After reading I[ssue #26](https://github.com/BurntSushi/nflgame/issues/26) on the project's Github page, I figured out how to populate all of the plays in a list.  To do this, we use the _list_ function:

plays = list(plays)

Grabbing the last play is easy after we have all of the plays in a list:

lastplay = plays\[len(plays)-1\]

If we print out lastplay, we get:

<nflgame.game.Play object at 0x0000000003D3B4A8>

This isn't very helpful, though.  To see the properties of that object, I created a really useful function:

def props(cls):   
  return \[i for i in cls.\_\_dict\_\_.keys() if i\[:1\] != '_'\]

Any time I'm not sure what properties are part of a variable, I'll set up that function.  It is incredibly useful for troubleshooting.  If I run _props(lastplay)_ I get the output: \['down', 'passing\_twopta', 'time', 'drive', 'passing\_twoptmissed', 'note', 'players', 'playid', 'yards\_togo', 'yardline', 'team', 'touchdown', 'home', 'data', 'events', 'desc'\] That means I can run lastplay.down, lastplay.passing\_twopta, lastplay.time, etc.  The one I care about here is _team_.  I change the subject of my alert to be 'Team Scoring Play':

lastplay.team + ' Scoring Play'

I let the body of the message be 'Score : Team Name Play Description':

str(g.nice_score()) + ' : ' + lastplay.team + ' ' + lastplay.desc

In the end, the if statement for a scoring play is:

if len(scores) > scores0: # someone scored
    text = str(g.nice_score()) + ' : ' + lastplay.team + ' ' + lastplay.desc 
    send(lastplay.team + ' Scoring Play', text)

    print text
    
    scores0 += 1

### Turnovers

For turnovers, I want the subject to be 'Team Name Turnover # (number)' and the body to be a description of the play.  To do this, the If statement will be similar to the last one:

if hometo > hometo0:
    text = lastplay.desc
    send(lastplay.team + ' Turnover #' + str(hometo), text)
    
    print "Home turnover number: " + str(hometo)
    
    hometo0 = hometo

 

### Penalties

Again, this will be similar to the others:

if homepenalty > homepenalty0:
    text = lastplay.desc
    send(lastplay.team + ' Penalty for ' + str(homepenyds - homepenyds0) + ' yds', text)
    
    print 'Home penalty for ' + str(homepenyds - homepenyds0) + ' yards.'
    
    homepenalty0 = homepenalty
    homepenyds0 = homepenyds

This will send me an email with the subject '(Team name) Penalty for (number of yards) yds', where the body is a description of the play. The final step is to schedule this to run (as we went through [here](http://www.techtrek.io/schedule-a-task-with-windows/)). I'm excited to see how this works during a game.  Unfortunately, I won't be around for the next game so if it doesn't work, I'll be out of luck.  The suspense of that is a lot of fun for me, though, so I can't wait!   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._