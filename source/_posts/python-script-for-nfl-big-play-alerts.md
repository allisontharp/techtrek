---
title: Python Script for NFL Big Play Alerts
tags:
  - nfldb
  - Python
url: 1701.html
id: 1701
categories:
  - Personal Projects
  - NFLdb
date: 2016-09-06 08:00:57
---

Football season officially starts this week!  To celebrate, I wrote a quick Python script to send me an email and/or text message any time something 'major' happens in a Colts game.  Right now, this script considers scoring, turnovers, and penalties as 'major' events.  This was a pretty quick script for me to write.  I hope to make the notifications better in the future.  The goal of this script was to simply get the base functionality of seeing an event and sending an alert working.

The Script
----------

You can grab a copy of the script from my Gist [here](https://gist.github.com/allisontharp/022e1f5a2ecde8d3661133817deeb0d3). For this, we will need to install two Python modules:

pip install nflgame
pip install smtplib

I've discussed the _nflgame_ [module](http://www.techtrek.io/nfldb-part-1-creating-an-nfl-stats-database/) before.  In case you missed it, this is an awesome module that pulls in NFL stats data from NFL Game Center. Import those two modules as well as the built in module _time_:

import nflgame as n
import time, smtplib

Next, we will write a function to send an email:

def send(SUBJECT, TEXT):
    gmail_user = 'you@emailaddress.com'
    gmail_pwd = 'YourVerySecurePassword'
    
    server = smtplib.SMTP("smtp.gmail.com", 587)
    server.ehlo()
    server.starttls()
    server.login(gmail\_user, gmail\_pwd)
    
    FROM = 'This@email.com'
    TO = \['one@email.com','two@email.com'\]
    
    message = """\
From: %s
To: %s
Subject: %s
\\n
%s
""" % (FROM, ", ".join(TO), SUBJECT, TEXT)
    
    # Send the mail
    print message
    server.sendmail(FROM, TO, message)
    server.quit()

The input for this function will be the subject of the email as well as the body of the email.  Insert your own gmail email address and password into the first two lines of the password.  The next four lines (that start with _server_), set up the SMTP server and log you in.  Next, put the email address you want the email to come from (typically, this should be your own email).  Then, in the _TO_ variable, put the email address(es) you want to send the message to.  Even if you only want to send the email to just one address, you still need to keep this variable as a list.  If you want your alert to be texted, you can email a text to your phone by doing something similar to '10digitnumber@txt.att.net' (Google your phone provider to see the exact format). Next, we will prepare the message.  Be careful here!  The message string cannot contain any tabs.  If you define a function (like I have), make sure there is no white space before each line in the message. Finally, we will print out the message, send the email, and quit the server. Next, we will initialize our variables:

g = n.one(2016, 4, 'CIN', 'IND', kind='PRE')
scores0 = len(g.scores)
hometo0 = g.stats_home\[6\]
awayto0 = g.stats_away\[6\]
awaypenalty0 = g.stats_away\[4\]
homepenalty0 = g.stats_home\[4\]
awaypenyds0 = g.stats_away\[5\]
homepenyds0 = g.stats_home\[5\]

First, we get the game data for the game we want.  In this instance, I am getting game data for the Indianapolis vs Cincinnati game in the 4th week of the 2016 preseason and setting it to the variable _g_.  Next, we will get the current number of scoring plays (_scores0_), number of home/away team turnovers (_home/awayto0_), number of home/away penalties (_home/awaypenalty0_), and finally, the number of yards that resulted from home/away penalties (_home/awaypenyds0_). The rest of the script runs while the game is still in progress.  To check if the game is in progress, we use _g.game_over()_.  If this object is False, the game is ongoing:

while(not(g.game_over())):
    g = n.one(2016, 4, 'CIN', 'IND', kind='PRE')
    games = n.games(year=2016, week=4, kind='PRE', away='IND')
    
    
    scores = g.scores
    
    hometo = g.stats_home\[6\]
    awayto = g.stats_away\[6\]
    awaypenalty = g.stats_away\[4\]
    homepenalty = g.stats_home\[4\]
    awaypenyds = g.stats_away\[5\]
    homepenyds = g.stats_home\[5\]
    
    if len(scores) > scores0: # someone scored

        text = str(scores\[len(scores)-1\]) + ' ' + str(g.nice_score())
        
        send('Scoring Play', text)

        print text
        
        scores0 += 1
    
    if hometo > hometo0:
        send('Home Turnover', str(hometo))
        
        print "Home turnover number: " + str(hometo)
        
        hometo0 = hometo
        
    if awayto > awayto0:
        send('Away Turnover', str(awayto))
        
        print "Away turnover number: " + str(awayto)
        
        awayto0 = awayto
    
    if awaypenalty > awaypenalty0:
        send('Away Penalty', str(awaypenyds - awaypenyds0))
        
        print "Away penalty for " + str(awaypenyds - awaypenyds0) + " yards."
        
        awaypenalty0 = awaypenalty
        awaypenyds0 = awaypenyds
        
    if homepenalty > homepenalty0:
        send("Home Penalty", str(homepenyds - homepenyds0))
        
        print 'Home penalty for ' + str(homepenyds - homepenyds0) + ' yards.'
        print
        
        homepenalty0 = homepenalty
        
        homepenyds0 = homepenyds
    
    time.sleep(120)

We grab the game stats again by calling the same line as before:

g = n.one(2016, 4, 'CIN', 'IND', kind='PRE')

We do this so that we can get any of the plays that just happened (in the end, we will do this every 2 minutes).  We also grab the game stats using the line:

games = n.games(year=2016, week=4, kind='PRE', away='IND')

The objects _n.one_ and _n.game_ behave slightly differently and can provide slightly different stats.  I wanted a few things from both, so I called the game stats using both methods.  There is probably a better way to do this with the nflgame module, but I wanted to get the script done before our game started. Next, I grab all of the current current scoring plays, turnovers, and penalties:

scores = g.scores
hometo = g.stats_home\[6\]
awayto = g.stats_away\[6\]
awaypenalty = g.stats_away\[4\]
homepenalty = g.stats_home\[4\]
awaypenyds = g.stats_away\[5\]
homepenyds = g.stats_home\[5\]

Notice that this is exactly what we did before to initialize our variables.  The next five If statements check to see if any of the variables were updated.  If any of them were updated, an email is sent using the _send_ function with the appropriate subject and body to the message.  After all If statements are checked, we wait 2 minutes and do the loop again.  When the game is over, the While loop will exit and an email will be sent with the final score.

Next Steps
----------

I'd like the body in the alerts to be a little bit better.  Right now, the emails typically just say, for instance, 'Home Turnover' with the body giving the current number of turnovers.  I would like to change that to have the team name instead of Home/Away as well as who turned the ball over, where it was at, etc.  Eventually, it would be cool to integrate this into a Twitter account (this idea is not new and already exists, but it could be fun to do that project myself).   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._