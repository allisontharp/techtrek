---
title: Blink Lifx Lights When Your Team Scores
tags:
  - powershell
url: 1996.html
id: 1996
categories:
  - Uncategorized
date: 2019-01-07 08:00:26
---

My dad and brother in law are in a friendly war for who can do the coolest thing with smart bulbs.  My dad recently had an idea that he would like to blink his lights whenever the Colts score.  Had he wanted to do this just a few months ago, it would have been really easy through IFTTT's ESPN plugin.  However, the ESPN plugin has since been killed off and this left my dad not sure how to proceed.  So, he called on his "IT Department" to see what we can pull together (apparently his name for me and Troy..). I wrote the following program during the last Wildcard game of the weekend (Eagles vs Bears).  There are a lot of features I'd like to add and it definitely needs cleaned up (some of which is documented at the end of this post), but I'm happy with where it is for a quick project!

### **Prerequisites**

*   [Lifx bulb](https://www.lifx.com/), preferably one that changes colors (could also work with Hue with some changes here and there)
*   [IFTTT](http://ifttt.com) account

### **Overview**

I used **Powershell** to ping nfl.com every 10 seconds.  During each run, it saves the previous scores of each team playing that I want to track and gets the current scores.  If my team scored, it will blink the Lifx lights the number of points scored and of the color of the team.  If the opposing team scored, the lights will blink red once.  I'm also a hockey fan, so I added capabilities for following NHL teams.

### **Functions**

**Getting Scores** There are two main functions to get scores, one each for NFL and NHL.  The output of both of these functions are of the same form so that I don't have to handle the leagues differently in the rest of the scripts.  Below is the code for getting NFL scores.

function get-NFLScores(){     $GamesArray = New-Object System.Collections.ArrayList          $url = "http://www.nfl.com/liveupdate/scores/scores.json"      $scores = Invoke-WebRequest -Uri $url | ConvertFrom-Json      $gameIDs = $scores | Get-Member | Where-Object MemberType -eq NoteProperty | Select-Object Name      foreach ($game in $gameIDs){         $nflObject = $scores.$($game.Name)          $gamesObject = New-Object System.Object         $gamesObject | Add-Member -MemberType NoteProperty -Name GameID -Value $($game.name)         $gamesObject | Add-Member -MemberType NoteProperty -Name League -Value "NFL"         $gamesObject | Add-Member -MemberType NoteProperty -Name GameStatus -Value $($nflObject.qtr)                  $gamesObject | Add-Member -MemberType NoteProperty -Name Home -Value $($nflObject.home.abbr)         $gamesObject | Add-Member -MemberType NoteProperty -Name HomeScore -Value $($nflObject.home.score.T)         $gamesObject | Add-Member -MemberType NoteProperty -Name Away -Value $($nflObject.away.abbr)         $gamesObject | Add-Member -MemberType NoteProperty -Name AwayScore -Value $($nflObject.away.score.T)          $GamesArray += $gamesObject              }     return $GamesArray }

The main thing here is that I'm taking the json output of www.nfl.com/liveupdate/scores/scores.json and converting it into a custom object that I defined.  Again, this is so that I can do the same thing with NHL data and handle the outputs the same way! **Finding Scoring Plays** Every 10 seconds, I store the last run's current score as a 'previous score' and get a new current score.  I then compare the two to see if a team scored and if so, how many points.  To do this, I have a comparison function:

function compare-Scores($PreviousScores, $CurrentScores){     $Output = New-Object System.Collections.ArrayList          foreach($game in $PreviousScores){         $GameID = $game.GameID         $CurrentScoreForGame = $CurrentScores | Where-Object {$_.GameID -eq $GameID}         $HomeDiff = $CurrentScoreForGame.HomeScore - $Game.HomeScore         $AwayDiff = $CurrentScoreForGame.AwayScore - $Game.AwayScore          if($HomeDiff -gt 0){             $Output += @{TeamName = $($game.Home); Points = $HomeDiff; Color = "blue"}         }          if ($AwayDiff -gt 0){             $Output += @{TeamName = $($game.Away); Points = $AwayDiff; Color = "orange"}         }     }      $Output }

This simply goes through each game in the $PreviousScores variable and compares the home and away scores for each.  The output is the team name that scored, the points they scored, and their color.  The color is currently hard coded as either Blue or Orange, but could really easily be updated to be dynamic (the data is there in the script, I just wrote it quickly..). Because both NFL and NHL data are formatted the same, I can have just one compare-Scores function! **Put It Together** First, I needed to get my [IFTTT webhook url](https://ifttt.com/maker_webhooks).  Then, I added a few teams that I want to watch, their names (following the standards for NFL and NHL below), and their colors. I get the current NFL and NHL scores, then limit to just the teams that I want to follow and only those that are currently playing. After that, I start a loop for 60 minutes where I get the new current score, look for a score by my team, then set the new Previous Scores to the Current Scores. If I found a score, I make a POST webrequest to my IFTTT Maker URL.  IFTTT does the rest of the work from there!

$MakerURL = "https://maker.ifttt.com/trigger/{YourTriggerName}/with/key/{YourKey}"  $MyTeams = New-Object System.Collections.ArrayList $MyTeams += @{League = "NFL"; Team = "IND"; Color1 = "blue"; Color2 = "white"} $MyTeams +=  @{League = "NHL"; Team = "Tampa Bay Lightning"; Color1 = "blue"; Color2 = "white"} $MyTeams += @{League = "NFL"; Team = "LAC"; Color1 = "yellow"; Color2 = "blue"} $MyTeams +=  @{League = "NHL"; Team = "Chicago Blackhawks"; Color1 = "yellow"; Color2 = "green"}  $TimeToRun = 60 #minutes $RunTime = 0  $TeamsToMonitor = ($MyTeams | Select-Object @{Name="TeamName"; Expression = {$_.Team}}).TeamName  $CurrentScores = get-NFLScores  $CurrentScores += get-NHLScores $CurrentScores = $CurrentScores| Where-Object {$_.Home -in $($TeamsToMonitor) -or $_.Away -in $($TeamsToMonitor) -and $_.GameStatus -ne "Final"} $PreviousScores = $CurrentScores  while ($RunTime -le $TimeToRun*60){     Remove-Variable CurrentScores     $CurrentScores = get-NFLScores      $CurrentScores += get-NHLScores     $CurrentScores = $CurrentScores| Where-Object {$_.Home -in $($TeamsToMonitor) -or $_.Away -in $($TeamsToMonitor) -and $_.GameStatus -ne "Final"}     Start-Sleep -Seconds 10     $RunTime += 10     $PreviousScores = $CurrentScores      $Scores = New-Object System.Collections.ArrayList     $Scores += compare-Scores -PreviousScores $PreviousScores -CurrentScores $CurrentScores     foreach($score in $Scores){         if($score.TeamName -in $TeamsToMonitor){             "Yay! $($score.TeamName) scores!"             $body = @{value1="$($score.Points)"; value2="$($Score.Color)"}             Invoke-WebRequest -Uri $MakerURL -Method Post -Body (ConvertTo-Json $body) -ContentType application/json         } else {             "Boo! No $($score.TeamName) Score!"             $body = @{value1="1"; value2="red)"; value3="5"} #Value3 could be transition\_duration or fade\_out_duration         }     } }

The script can be found in its entirety on my GitHub [here](https://github.com/allisontharp/ScoreBird).

### **IFTTT Integration**

The final step here is to create the IFTTT recipe.  The 'this' portion of the recipe will be a Webhook, which is what you trigger with your Maker URL.  The 'then' part is Lifx: ![](/wp-content/uploads/2019/01/Image-1-497x1024.jpeg)

### **Next Steps**

*   Clean up the $MyTeams input
*   Put in the cloud as an Azure Function or some other Serverless Framework method
*   Update to have the lights fade off and back on slowly when opposing team scores
*   Add ability to follow other leagues (specifically ECHL and NBA)
*   Make the colors dynamic and not hard coded
*   Blink between the team's primary and secondary colors
*   Risks
*   *   If the NFL or NHL change the way their website is formatted or the URL of their websites, this will break

Hopefully next weekend I get to see lots and lots of blue blinking lights! _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._