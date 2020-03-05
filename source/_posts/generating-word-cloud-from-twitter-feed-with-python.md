---
title: Generating Word Cloud From Twitter Feed with Python
url: 1778.html
id: 1778
categories:
  - How To
date: 2016-10-07 08:00:02
tags:
---

Every week, someone on Reddit posts a "word cloud" on all of the NFL team's subreddits.  These word clouds show the most used words on that subreddit for the week (the larger the word, the more it was used).  These word plots are always really fascinating to me, so I wanted to try to make some for myself.  In this tutorial, we'll be making the following word cloud from my board game stats twitter feed, [@BGGStats](http://www.twitter.com/bggstats) ![tweetcloud2](http://www.techtrek.io/wp-content/uploads/2016/10/tweetcloud2-1024x768.png) First, we will import the following modules.  If there are some of these that you don't have, go ahead and install them via pip.

import tweepy, json, random
from tweepy import OAuthHandler
import matplotlib.pyplot as plt
from wordcloud import WordCloud, STOPWORDS
from scipy.misc import imread

Next, I will define a function that outputs a random hue saturation color value for a random shade of gray:

def grey\_color\_func(word, font\_size, position, orientation, random\_state=None, **kwargs):
    return "hsl(0, 0%%, %d%%)" % random.randint(60, 100)

We'll use this later to set the colors of the words. Next, we'll set up the OAuth for twitter and feed it into the API:

consumer\_key = 'your\_consumer_key'
consumer\_secret = 'your\_consumer_secret'
access\_token = 'your\_access_token'
access\_secret = 'your\_access_secret'
 
auth = OAuthHandler(consumer\_key, consumer\_secret)
auth.set\_access\_token(access\_token, access\_secret)
  
api = tweepy.API(auth)

You can get your consumer and access keys and secrets at https://apps.twitter.com/. Now that we have our Python script authorized to use our Twitter account, we can grab all of our tweets.  Because I think I'll likely want to test a few different visualization options, I went ahead and dumped all of my tweets into a file called _tweets_.  This way, if I want to run the script several times, I won't have to rescrape the data from Twitter.

f = open('tweets', 'w')    

for status in api.user_timeline():
    f.write( api.get_status(status.id).text)
    
f.close()

Open that file up and combine all of the words in the file into one variable (called _words_).  When we want to run this script in the future, everything prior to this point can be commented out!

words=' '
count =0
f = open('tweets', 'r')
for line in f:
    words= words + line
f.close

The WordCloud function has the ability to leave out some words.  We can create a list of words to feed in to ignore.  I suggest you keep the list empty the first time you generate the wordcloud so you get an idea of what words you might want to omit.  For me, the only word I wanted to omit was 'will':

stopwords = {'will'}

WordCloud also allows you to use a mask.  The mask should be a .png file where the transparent portion is what you want your shape to be.  I used the twitter logo just to test out the functionality.  Put the .png file in the directory where your script is and add the following line:

logomask = imread('twitter_mask.png')

We can finally create the word cloud!  The function can take several arguments (you can view the entire list [here](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html#wordcloud.WordCloud)).  I tried to use most of them just to get a feel for how the module works:

wordcloud = WordCloud(
    font_path='Xerox Sans Serif Wide Bold Oblique.ttf',
    stopwords=STOPWORDS.union(stopwords),
    background_color='black',
    mask = logomask,
    max_words=500,
    width=1800,
    height=1400
).generate(words)

The only tricky thing here is that you'll need to put your font .ttf file in your directory (or navigate to it).  You can use Google to find sites that allow you to download .ttf files for your favorite fonts.  The rest of the arguments are pretty self explanatory. Finally, we'll use matplotlib to generate the word cloud:

plt.imshow(wordcloud.recolor(color\_func=grey\_color\_func, random\_state=3))
plt.axis('off')
plt.savefig('./tweetcloud2.png', dpi=300)
plt.show()

That's it!  I want to research more into how to set custom colors for the words.  The documentation for the module makes it seem like the module might be able to copy colors from an image, which could be really cool.  You can see an example of that [here](https://amueller.github.io/word_cloud/auto_examples/masked.html#masked-py). _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._