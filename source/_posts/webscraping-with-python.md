---
title: WebScraping With Python
tags:
  - Automation
  - boardgames
  - Python
url: 1644.html
id: 1644
categories:
  - How To
date: 2016-08-12 08:00:48
---

This past weekend, I wanted to track the [BoardGameGeek GeekBuzz Leaderboard](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwizlqX3lLnOAhWCRyYKHUJzC2UQFggiMAE&url=https%3A%2F%2Fboardgamegeek.com%2Fgeekbuzz%2Fmobile%2Fleaderboard&usg=AFQjCNE5Gb1TfFuK73YDbpl76BaBYyicvg&sig2=Lw1CxNJlym6Z8KcYz9CPOA&bvm=bv.129422649,d.eWE) every hour during GenCon.  However, this website is dynamic and therefore I couldn't easily get the game names or rank from the source code.  In order to get the information I wanted, I need to get Python to actually launch a web browser and browse the webpage as a human user would. In order to do that, I used a module called **selenium** (pip install selenium if you do not have it).

### Initialize The Driver

First, we need to import the modules and initialize the driver.  Import the modules:

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException

I made a function called _init_driver()_ so that I could call it any time.  The function looks like:

def init_driver():
    driver = webdriver.Firefox()
    driver.wait = WebDriverWait(driver, 5)
    return driver

This function opens Firefox in a window, waits 5 seconds for it to load, then returns the driver.

### Get All Games In The Leaderboard

Next, I made a function to grab all of the games:

def lookup(driver, query):
    games = \[\]
    driver.get("https://www.boardgamegeek.com/geekbuzz/mobile/leaderboard")
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(1)
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(1)
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(1)
    for a in driver.find\_elements\_by\_tag\_name('a'):
        if a.text not in \['GeekBuzz','Add Item ', 'Home', 'Leaderboard', ' Login', ''\]:
            if a.text.encode('utf-8') not in games:
                games.append(a.text.encode('utf-8'))
    
    driver.close()
    return games

First, it initializes the output as an empty list.  Then, it has the open FireFox window navigate to the webpage I care about (the GeekBuzz Leaderboard).  The Leaderboard only shows about 50 games on the page.  In order to get all 200ish games, the user needs to scroll down and wait for the page to load.  The next 6 lines do exactly this (scroll to bottom, wait a second for the page to load, scroll to bottom, etc). There are a set number of games in the leaderboard, so I knew I only had to scroll to the bottom three times  Once I reached the very bottom, I was then able to grab all of the game names. If we look at the website, we see that the game names are actually links to another page.  I know that in HTML, a link has a tag name 'a'.  Therefore, I loop through the entire page and I grab all of the link names by looking for the text of elements with the tag name 'a'.  However, there are some links that are always on the page that I don't care about, such as one called GeekBuzz, Add Item, Home, etc.  If the link has a different name than that, I add it to my games list. Once the script has gone through all of the links on the page, it closes the FireFox browser and returns the list. The script goes on to add this data to the database as well as email and text me if a game increased in rank by more than 5. This was a cool little project, but unfortunately it didn't get done until Saturday.  This meant that I missed out on a lot of cool data from GenCon (Thursday - Saturday).  I'm excited to have this script ready for other conventions, though!   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._