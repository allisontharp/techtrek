---
title: Creating A Reddit Bot
url: 1822.html
id: 1822
categories:
  - How To
date: 2017-01-07 21:38:18
tags:
  - Python
---

Happy New Year!  It has been quite a long while since I last wrote a blog post.  After making it 100 straight blog posts without missing a Tuesday or Friday deadline, I decided to take a little break from blogging.  However, I've done a few exciting programming and database related things in that time, and I want to share that information!

### Stat-Bot Overview

A couple of months ago (or so..), I decided I wanted to create a Reddit bot.  Reddit is a social media website composed of different communities (called _subreddits_).  A Reddit bot, in general, reads all of the posts and/or comments on specific subreddits (or all subreddits) and when it is summoned, it responds in some manner.  There are a lot of really cool Reddit bots out there already (some examples include one that converts measurements in the metric system to numbers that us Americans can understand, and there is one that you can call to remind you about a post in a set amount of time). My bot is called stat-bot.  Currently, the bot monitors comments in its own community ([/r/statbot](https://www.reddit.com/r/statbot/)) that call it.  A sample call is:

/u/stat-bot mad4hatter plays

The general formula for this is /u/stat-bot \[BGG Username\] plays.  I have the required "plays" word at the end because I hope to add other cool stats in the future (such as looking at people's collections).  In addition, users can give a date range (such as /u/stat-bot mad4hatter plays 2016-01-01 2016-12-31). The bot responds within a few minutes with the total number of plays, the total play time, and their top 10 most played games in that time frame.  A sample output of this would be (the formatting looks a LOT better on Reddit!):

mad4hatter's play summary from 2016-01-01 - 2016-06-30:
Total Plays: 199
Total Time: 8111 min (135.18 hours)

Game	Total Plays	AVG Minutes	AVG Score For Your Logged Plays	Your AVG Score
Dominion		9	26.67	34.94	32.0
6 nimmt! Plus		7	30.86	45.81	50.86
Piece o Cake		7	19.14	27.0	30.0
Ninja Camp		7	21.29	31.6	33.14
Diamonds		6	32.33	37.41	37.67
Patchwork		6	27.83	17.0	20.25
Fairy Tale		5	16.2	42.86	44.6
Valeria Card Kingdoms	5	55.0	48.58	53.5
Kodama The Tree Spirits	5	50.2	115.69	124.2
Too Many Cinderellas	4	37.33	1.67	2.0

### Python Overview

You can view all of the code [here](https://github.com/allisontharp/statbot/blob/master/main.py). Every 15 minutes, the bot checks all of the comments on the statbot subreddit.  It only looks at comments that it hasn't yet commented on (based on comment id) and for comments that have its name in it (/u/stat-bot).  If it finds a comment like that, it grabs the commend id, the Reddit username that posted the comment, and the comment itself and puts it into a SQLite database called _comments_.  This portion of the code is below:

while True:
    r = obot.login()
    subreddit = r.get_subreddit('statbot')
    all\_comments = subreddit.get\_comments()
    c, conn = initsql()
    
    comm\_replied\_to = \[\]
    
    query = "SELECT commentid FROM comments"
    out = sql(c, query)
    comm\_replied\_to = \[str(i\[0\]) for i in out\]
    
    for comment in all_comments:
        if comment.id not in comm\_replied\_to: # and comment.author.name != 'stat-bot:
            body = comment.body
            body = body.replace('\\n', ' ')
            body = re.sub('\["\]','',body)
            body = body.split(' ', 10000)
            body = \[i for i in body if i != ''\] # remove any spaces
            
            if '/u/stat-bot' in body:
               # print body

                condensed = ' '.join(body)

                query = 'INSERT INTO comments (commentid, username, comment) VALUES ("{cid}","{usr}", "{cmt}")'.format(cid = comment.id, usr = comment.author, cmt = condensed)

Next, it removes everything before the bot call (/u/stat-bot).  The BGG username _should_ be the next word after the bot call, so it grabs that word and puts it into a variable called **username**.  If the username is _about_, it comments a general comment about what it does and where you can find its code.  If the username is _help_, it gives a sample call. Otherwise, the kind of call should be next (for now, it only knows "play").  The next word is optional, but if it is there, it would be the minimum date (so it is stored in a variable called **mindate**).  Likewise, the word following that is optional and stored into a variable called **maxdate**. After I have parsed the input, I start checking for errors.  The first check is to make sure the dates are valid.  All of that code is here:

ind = body.index("/u/stat-bot")
                # Remove everything before the bot call:
                for i in xrange(ind+1):
                    body.pop(0)
                
                username = body\[0\]
                kind = ''
                if username == 'about':
                    out = '''Hello!  I am a bot that grabs play data for plays logged on board game geek for a specific user.
\\n\\nTo call me correctly, use the format:\\n\\n/u/stat-bot your_username plays startdate enddate \\n\\nex: /u/stat-bot Octavian plays 2016-1-1 2016-06-30
\\n\\nMy code is available \[here\](https://github.com/allisontharp/statbot).
\\n\\nI am always interested in suggestions for improvement.  If you have any suggestions, please message me!'''
                elif username == 'help':
                    out = 'To call me correctly, use the format:\\n\\n/u/stat-bot your_username plays startdate enddate \\n\\nex: /u/stat-bot Octavian plays 2016-1-1 2016-06-30'
                else:
                    error = \[\]
                    try:
                        kind = body\[1\] ## play or collection
                    except IndexError:
                        error = \[\]
                    try: 
                        mindate = body\[2\] ##
                    except IndexError:
                        mindate = ''
                        
                    try:
                        maxdate = body\[3\]
                    except IndexError:
                        maxdate = ''
                    
                    try:
                        mindate = validate_date(mindate)
                    except ValueError:
                        error.append("- Start date format incorrect.  Expected YYYY-MM-DD, received '{md}'.".format(md = mindate))
                        
                    try:
                        maxdate = validate_date(maxdate)
                    except ValueError:
                        error.append("- End date format incorrect.  Expected YYYY-MM-DD, received '{md}'.".format(md = maxdate))    

If the call is correct for plays_, _it goes to a function called **playsmain**.  If the user called collection (which will be available in the future), it goes to a function called **collectionmain**.  If the user called anything else, it comments back to the user that it didn't understand the input. If there are errors, it comments back a list of all of the errors to the user in addition to giving the user a sample call. Once the reply is ready to be sent, it sends out the reply via the **comment.reply(out)** command and inserts into a SQLite database what it responded.  It waits 30 seconds between each comment that it reads.  When it has read all of the new comments, it waits 15 minutes before checking for more new comments.  This code is here:

if error == \[\]:
                        isfail = 0
                        if kind == 'plays':     
                            out = playsmain(c, username, mindate = mindate, maxdate = maxdate)
                        elif kind == 'collection':
                            out = collectionmain(c, username, conn = conn)
                        else:
                            print "Someone mentioned /u/stat-bot, but didn't call it correctly"
                            out = "You didn't mention what kind of stats you want, or I am having trouble understanding you.  Right now, I only know plays.  Please use '/u/stat-bot help' if you need help."
                    else:
                        isfail = 1
                        out = "It looks like we've had one or more errors:\\n\\n"
                        out += '\\n\\n'.join(error)
                        out += "\\n\\nTo call me correctly, use the format:\\n\\nyour_username plays startdate enddate \\n\\nex: /u/stat-bot mad4hatter plays 2016-01-01 2016-06-30"
                    
                
                #print out
                comment.reply(out)
                print "Bot replied to : ", comment.body
                query = '''INSERT INTO comments (commentid, username, comment, isfail, bggusername) VALUES
("{cid}", "{usr}", "{cmt}", {isfail}, "{bgg}")'''.format(cid = comment.id, usr = comment.author, cmt = comment.body, isfail = isfail, bgg = username)
                
                conn.commit()
                time.sleep(30)
    
    time.sleep(900)

### Future Work

I definitely want to flesh this bot out more.  This bot is unlike anything else I've ever programmed before.  I had to really anticipate ways that other people would misuse the commands and flesh out error messages, output messages, input commands, etc so that hopefully the average user could understand.  It also feels insanely good to interact with a bot that you wrote yourself.  If you haven't ever done something like this, I definitely recommend it! As far as future work on /u/stat-bot, I want to add fun things about users collections (such as their most owned mechanic, publisher, artist, etc).  I also want to add comparison stats (such as "You have played X games since you asked me last" or "You have played Y more games than the requester").  The opportunities are endless with this bot, and I definitely want to pursue them! _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._