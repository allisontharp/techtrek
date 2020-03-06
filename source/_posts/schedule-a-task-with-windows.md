---
title: Schedule a Task with Windows
tags:
  - Administration
  - boardgames
url: 1359.html
id: 1359
categories:
  - How To
date: 2016-06-03 08:00:19
---

I've posted a lot lately about my board game database.  As mentioned in the [first post](http://www.techtrek.io/board-game-geek-database/), one of my main goals from the beginning was to be able to run a script at the beginning of every month to add the current rank and number of BGG users owning each game as well as adding any new games.  Even though I was able to speed the script up, I still would like to start the script ASAP on the first of the month.  I could obviously stay up until midnight and run the script manually.  However, I thought this would be a great opportunity to learn about the Window's Task Scheduler. The Task Scheduler is an application built in to Windows that lets you launch a program or script at a pre-defined time (or in pre-defined time intervals).

#### Using The Task Scheduler

To open the Task Scheduler, press the Windows key and search 'Task Scheduler'. Next, go to the **Action** menu and click **Create Basic Task...** ![Capture](/wp-content/uploads/2016/05/Capture-5.jpg) In the window that pops up, give the task a name and description then click Next. ![Untitled](/wp-content/uploads/2016/05/Untitled.png) The next window is self-explanatory.  Choose how often you want the script to run.  For me, I want it to run once a month so I choose Monthly and click Next.![Untitled](/wp-content/uploads/2016/05/Untitled-1.png) Use the next page to select in more detail when you want the script to run.  For me, I want it to start on the first of June at 12:05 AM and run on the first of every month. Next, we choose what _action_ we want to do.  Our options are start a program, send an email, or display a message.  I want to start a program, so I choose that. I want to run a Python script.  The the Program/script, we need to navigate to the Python.exe file.  For me, this is located at _E:\\Python27\\python.exe_.  In the Add arguments field, I pasted the path and file name of the Python script I want to run.  Finally, for the Start in field, I put the same path (without the file).  I need to do this because my Python script needs to be run from the same folder where the board game database is. Finally, click Next then Finish. If you want to see a list of all of your tasks, click the Task Scheduler Library on the left side of the window: ![Capture](/wp-content/uploads/2016/06/Capture.jpg) You can test any task by right clicking and selecting Run. That's it!  This was a super simple way for me to easily run my script at the beginning of every month.  I'm excited to see my board game database grow and using the Task Scheduler will surely help me in my journey. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._