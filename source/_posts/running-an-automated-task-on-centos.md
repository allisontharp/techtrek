---
title: Running an Automated Task on CentOS
tags:
  - boardgames
  - Linux
  - Python
  - SQLite
url: 1626.html
id: 1626
categories:
  - How To
date: 2016-08-05 08:00:10
---

I am interested in moving my [BGG Hotness Ratings](http://www.techtrek.io/tracking-bgg-hotness-rankings-during-gencon/) from my local machine to a hosted server, so that I can access it easier while at GenCon.  In order to do that, I copied my existing database and python script to my server using [WinSCP](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwihqODHh6TOAhVBRSYKHV56D3gQFggcMAA&url=https%3A%2F%2Fwinscp.net%2Feng%2Fdownload.php&usg=AFQjCNFGy0DbS4A__xFv8ToHgJYyYD3BOw&sig2=5bCOPA9u30CoJ6TPC3mhRg&bvm=bv.128617741,d.eWE) (I put them in /var/scripts/gencon/).  From there, I just needed to set up the automated task so that the script runs every hour.

### Overview Of Configuration File

The configuration file is located at _/etc/crontab_.  In order to view and edit it, open it with vim using the command:

vim /etc/crontab

When you first open the file, it should look like this:

SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

\# For details see man 4 crontabs

\# Example of job definition:
\# .---------------- minute (0 - 59)
\# |  .------------- hour (0 - 23)
\# |  |  .---------- day of month (1 - 31)
\# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
\# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
\# |  |  |  |  |
\# *  *  *  *  * user-name  command to be executed

The top line, _SHELL=/bin/bash_ tells the system which shell to use (by default, this is the bash shell).  Next, the _PATH_ variable defines the path used to execute the variable.  Note, this doesn't necessarily have to be where the script is.  Finally, the _MAILTO_ is where the output will be sent.  If you define this at an empty string (""), nothing will be sent.  In addition, you can add a _HOME_ variable, which will set the home directory for executing commands. Each line after this will represent a task and must contain the following, in the following order:

minute hour day month dayofweek command

For any of these sections, you can use an asterisk to represent all valid values (such as every second, hour, day, month, etc).  If you would like to do a range of values, you can use a hyphen (such as 1-4 for 1,2,3, and 4).

### Adding Your Task

For me, I want to run a Python script located in /var/scripts/gencon every hour.  First, I find out where Python is located by typing the command

which python

The output of this tells me that the Python executable is located in /usr/bin.  Next, within the crontab config file, I confirm that /usr/bin is listed within the PATH variable. I want my script to run out of the /var/scripts/gencon directory because the script relies on being in the same location as the database.  Because of this, I add the following line after the MAILTO variable:

PATH = /var/scripts/gencon

Finally, I add my task.  For me, this line looks like:

0 * * * * python main.py

This simply states that I want the main.py script to run every hour, of every day, of every month, of every day of the week.   With this done, I should be able to easily remote into my server while at GenCon and check up on the Hotness Ratings.  Be sure to follow me [@BGGstats](http://www.twitter.com/bggstats) to see any interesting data that comes out of this and to request specific stats.   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._