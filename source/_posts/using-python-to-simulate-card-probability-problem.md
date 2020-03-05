---
title: Using Python to Simulate Card Probability Problem
tags:
  - Python
  - statistics
url: 1679.html
id: 1679
categories:
  # - Programming
date: 2016-08-26 08:00:56
---

This post is a little different than my normal posts here.  However, it is something that I worked on recently, it was a ton of fun, and the results were shocking to me so I thought I'd share.  A board game designer friend of mine asked me a question recently that I found really interesting and couldn't quite figure out.  He had a problem: say you have five pairs of cards (1,1,2,2,3,3,4,4,5,5).  If you shuffle the cards and deal them out randomly to five people (so each person has two cards), what are the chances that two people have the same hand? It has been a long time since I've taken a probability or statistics course.  I couldn't remember how to do this problem, but I did know I could write a Python script to simulate the result.  The answer to the problem will be toward the end of this post.  If you are like me, you might want to think about what you feel like the answer should be before you finish the post!  He asked his friends on Facebook and got answers ranging from 0.04% to roughly 12%, so that might help you with your own answer.

### The Script

First, we will need to import two modules: random (to shuffle the cards) and Counter from collections (to count the similar hands).

import random as r
from collections import Counter as counter

Python is case sensitive and I'm lazy, so I tend to import modules with a capital letter as the same word but all lowercase (as I did here with _counter_). The flow of the script will be, in general, like this:

1.  Define a list with the 10 "cards" in it
2.  Shuffle the cards
3.  Turn the list of of 10 items into a list of 5 tuples, each tuple representing a person's hand
4.  Sort the cards in each tuple (so that a hand of 2,1 becomes 1,2.  This makes it easier to find matching sets since we don't care about the order.)
5.  Order the list of tuples (for the same reason as listed in step 4)
6.  Check if there are any matching sets.  If so, increment the counter.
7.  Repeat steps 2-6 N number of times

To "shuffle" out the "cards", we define a function called _chunks_:

def chunks(l, n):
    n = max(1, n)
    return \[l\[i:i + n\] for i in range(0, len(l), n)\]

This function takes a list, _l_, and will divide it up into a list of lists of size _n_. Next, we define our list of "cards", initialize our success counter, and define the number of trials we want to do:

cards = \[1,1,2,2,3,3,4,4,5,5\]

success = 0
trials = 100000
check = =

The rest of the script takes place within a for loop.  We will loop through however many times we define in our _trials_ variable.  In general, the higher the trial number the more accurate the result will be (this reaches an asymptote, so eventually a higher number will be insignificant to our results).  The for loop is:

for i in xrange(trials):

    r.shuffle(cards)
    arrays = chunks(cards, 2)  

    for x in xrange(len(arrays)):
        arrays\[x\] = tuple(sorted(arrays\[x\]))

    arrays = sorted(arrays)

    c = counter(arrays).most_common()

    for k,v in c:
        if v > 1 and check == 0:
            success += 1
            check = 1
    
    check = 0 

    print success*1.0/(i+1)

First, we shuffle the cards using the random module's shuffle function.  This is critical to our result -- if Python's shuffle function isn't truly random (or "random enough"), our result won't be valid.  I trust Python's random module and I believe the result is accurate. We then deal out the cards by passing them into our _chunks_ function.  I (stupidly) called this variable 'arrays'.  Right now, this variable is a list of lists (so, it might look like \[\[1, 4\], \[2, 1\], \[5, 3\], \[4, 2\], \[3, 5\]\]). Next, we loop through each "hand" and put the lower number first then make it be a tuple.  For our example above, the output would be: \[(1,4),(1,2),(3,5),(2,4),(3,5)\]. Now, we order our list of tuples: \[(1,2),(1,4),(2,4),(3,5),(3,5)\].  Both of these steps make it much easier to find the pairs. Now we use the _counter_ function.  This function looks at each element within the array and counts how many of each one there are.  The result, _c_, for us would be: \[ ((1,2),1) , ((1,4),1) , ((2,4),1) , ((3,5),2) \] We them loop through each element and grab the 'k' and 'v' values.  The _k_ value is simply the tuple (the "hand") and the _v_ value is the number of times the hand appears.  The trickiest part about the entire script is in this loop: we only want to count the first match.  If there are two sets of matching cards (for instance, two players have 1,2 and two others have 3,4), we don't want to count the 3,4.  We only care about the chances of _at least_ two players having a matching hand.  Therefore, we earlier defined a variable called _check_.  When we find our first marching pair (a _v_ value greater than 1), we will increment our _success_ counter and set our _check_ flag to 1.  Any time the check flag is 1, we won't increment our success counter.  After we loop through each hand, we reset our check value, print our the current check probability, and go again.

### The Results

Before looking at the results, what do you think the answer is?  My own gut feel thought that somewhere between 5 and 10% seemed reasonable.  The actual, mathematical result was shocking to me: there is a **25.3968%** chance that at least two players will have the same hand!  Below is a table with the number of trials and, as you can see, as we do more and more trials the results get closer and closer to the 25.3968% result. \[table width="500" " colalign="left|left"\] NumberOfTrials, Result 10, 0.3 100, 0.34 1000 ,0.252 10000, 0.2546 100000, 0.25656 1000000, 0.253796 10000000, 0.2539957 \[/table\]   _Were you surprised at the end result?  What did you think the chances were going to be?_ _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._