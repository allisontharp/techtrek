---
title: Blink Lifx Lights When Your Team Scores (Without IFTTT!)
tags:
  - powershell
url: 2013.html
id: 2013
categories:
  - Uncategorized
date: 2019-01-09 13:57:53
---

This week, I've been working on blinking smart bulbs in my house when my favorite professional sports team scores points (you can view the first iteration of this project in my previous blog post [here](/blink-lifx-lights-when-your-team-scores/)). This previous method _technically_ works but there is about a 45 second delay between a score and the resulting blinks.

This iteration completed most of the Next Steps items from the previous post. However, the biggest change was moving away from IFTTT and triggering the Lifx lights using the API directly.

### **Lifx API**

The first step is to generate an API token via the Lifx API [here](https://cloud.lifx.com/settings) ([https://cloud.lifx.com/settings](https://cloud.lifx.com/settings)). Keep this token safe and don't let others see it!

In my functions file, I created 3 new functions for controlling the lights: **invoke-setLight**, **invoke-Pulse**, and **invoke-Breathe**. To understand what the API was expecting, I followed the Lifx API documentation [here](https://api.developer.lifx.com/docs/introduction). As far as API documentation goes, this one is pretty good. Most functions have an interactive portion at the bottom which allows you to test it out yourself and also see what inputs the API expects.

    function invoke-setLight ($color, $brightness, $token){
        $url = "https://api.lifx.com/v1/lights/all/state"
    
        $header = @{
            "Authorization" = "Bearer $token"
        }
    
        $body = @{
            "power"="on";
            "color" = "$color";
            "brightness" = $brightness
        }
    
        Invoke-RestMethod -Headers $header -Uri $url -Body $($body|ConvertTo-Json) -Method Put
    }
    
    function invoke-Pulse ($primaryColor, $secondaryColor, $numberBlinks, $token){
        $url = "https://api.lifx.com/v1/lights/all/effects/pulse"
    
        $header = @{
            "Authorization" = "Bearer $token"
        }
    
        $body = @{
            "color" = "$primaryColor";
            "from_color" = "$secondaryColor";
            "period" = 1;
            "cycles" = $numberBlinks;
            "power_on" = $true;
            "persist" = $false
        }
        Invoke-RestMethod -Headers $header -Uri $url -Body $($body|ConvertTo-Json) -Method Post -ContentType application/json
    }
    
    function invoke-Breathe ($primaryColor, $token){
        $url = "https://api.lifx.com/v1/lights/all/effects/breathe"
    
        $header = @{
            "Authorization" = "Bearer $token"
        }
    
        $body = @{
            "color" = "$primaryColor";
            "period" = 10;
            "cycles" = 1;
            "persist" = $false;
            "power_on" = $true
        }
    
        Invoke-RestMethod -Headers $header -Uri $url -Body $($body|ConvertTo-Json) -Method Post -ContentType application/json
    
    }

A big drawback of using the API instead of IFTTT is that my Dad won't be able to turn ScoreBird on or off -- he will likely have to rely on me to do that. However, I think using the API will result in **much** faster reaction times and I don't mind turning the functionality on or off for him.

### **Additional Enhancements**

**Only monitor leagues with games going on**  
I updated the **get-CurrentScores** function to only ping the league API if there is actually a game I want to monitor going on. I did this because I don't want to ping NCAA Football or NFL on a night when the only team being monitored is an NHL team. It is good practice to try to only hit APIs as often as you need (and no more!), so I'm happy with this addition.

**Update lights to fade off and back on when opposing team scores**  
This is done via the **Invoke-Breathe** function described above. When the opposing team scores, the light fades to a deep, dark red and then back to the current state over the course of 10 seconds.

**Make colors dynamic (not hard coded)**  
Again, this is done via the functions described above. Instead of having the colors hard coded, the lights will blink depending on the team the scored and the colors defined in the $PeoplesTeamsObject

**Blink between the team's primary and secondary colors**  
This goes hand in hand with the previous addition. Instead of just blinking the primary color, I added the ability to alternate the blinking between the team's primary and secondary color. Many team's secondary color is white (or yellow -- which as a light, looks a lot like white), so this is a feature that will likely seldom be noticed, but I am happy to have it added.

**Multiple "Profiles"**  
I added the ability to track teams for multiple people. This way, I don't have to run two scripts if there are games going on that both me and my dad are interested in at the same time.

You can view the code for this project in its entirety on my GitHub [here](https://github.com/allisontharp/ScoreBird).

### **Next Steps**

*   Put in the cloud as an Azure function or some other Serverless Framework method
*   Add ability to follow other leagues (specifically ECHL and NBA)
*   Because the NHL ever only scores one point at a time and because they are generally low scoring games, instead of just blinking the number of points scored, blink the total number of points that the team has
*   Turn on automatically when a monitored team has a game starting

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._