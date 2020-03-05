---
title: 'Keyforge Data Scraping Part 2: The Script'
tags:
  - keyforge
url: 1919.html
id: 1919
categories:
  - Personal Projects
  - Boardgames
  - Keyforge
date: 2018-11-26 20:09:01
---

In the [previous post](http://www.techtrek.io/keyforge-data-scraping-part-1-the-api/) in this series, we discussed the API for the new procedurally generated card game called Keyforge.  In this post, we'll walk through a simple implementation of the API via PowerShell.  The script in its entirety can be found on my GitHub [here](https://github.com/allisontharp/Keyforge).

### Overview

The goal of this script was to run through all of the decks currently registered and save data about the decks and the cards in the decks.  This project was intended as a working proof of concept for a larger project I'd like to build out.  In the end, I'd like to have the data stored in Azure SQL Server and build a simple API and a simple PowerBI report in front of it so that users can get answers to their Keyforge data questions.

### Requirements

*   SQL Server ([Express edition is free](https://www.microsoft.com/en-us/sql-server/sql-server-editions-express))
*   PowerShell (Comes preinstalled on Windows machines)
*   PowerShell Module dbatools
    *   within PowerShell run: install-module dbatools

### The Script

The first part of the script checks to see if the database has been created or not.  If it hasn't been created, it creates the database:

if (-not (Find-DbaDatabase -SqlInstance $server -Pattern $database)) { # If the database doesn't exist
    New-DbaDatabase -SqlInstance $server -Name $database # create the database

Next, it tries to get information on as many of the cards as it can.  It does this by looping through the API pages and collecting distinct cards until it has 350 cards, 7 houses, or it gets to a page that hasn't had a new card.  This part of the code was more or less taken from BatmanAMA on GitHub and was used as a starting point.  

    #####################
    ##  Get Card Data  ##
    #####################
    $Url = "https://www.keyforgegame.com/api/decks/?page={0}&page_size={1}&search={2}&links=cards" -f "{0}", $pagesize, $search
    $page = 1
    $cards = \[System.Collections.Generic.List\[object\]\]::new()
    $houses = \[System.Collections.Generic.List\[object\]\]::new()
    do {
        $Response = Invoke-WebRequest ($url -f $page++) -ContentType 'application/json; charset=utf8'
        $jsonCorrected = \[Text.Encoding\]::UTF8.GetString(
                  \[Text.Encoding\]::GetEncoding(28591).GetBytes($Response.Content)
                )
        $decks = $jsonCorrected |ConvertFrom-Json
        if (($decks._linked.cards | where-object id -notin ($cards.id))) {
            $cards.AddRange(($decks._linked.cards | where-object id -notin ($cards.id)))
        }
        if (($decks._linked.houses | where-object id -notin ($houses.id)).count) {
            $houses.AddRange(($decks._linked.houses | where-object id -notin ($houses.id)))
        }
    } while ($cards.Count -lt $totalCards -or $houses.Count -lt $totalHouses -or $page * $pagesize -ge $decks.count)

A major issue I had early on in this process was that PowerShell wasn't bringing in special characters correctly with the default settings of Invoke-RestMethod.  Figuring this part out is what took, by far, the longest amount of time during this project. To get around the text issue, I used Invoke-WebRequest instead of RestMethod.  I added the ContentType parameter with the charset=utf8.  From there, I added the UTF8.GetSTring and updated the encoding.  This seemed to correct most or all of the issues I was having. The script now has 3 main objects that we care about:

*   $decks - information such as deck name and houses
*   $houses - house names
*   $cards - card names, traits, number, etc

However, the data isn't yet being stored anywhere!  Before we can store the data, we need to create the tables in the database (since we know the script created the database). The dimCards table will hold information on the individual cards.  Create this table:

    # Create dimCards table (without traits)
    $query = "CREATE TABLE dimCards (
    CardID INT IDENTITY(1,1) PRIMARY KEY
    , CardKeyforgeID VARCHAR(255)
    , CardNumber INT
    , CardName NVARCHAR(MAX)
    , House NVARCHAR(MAX)
    , CardType NVARCHAR(MAX)
    , FrontImageURL NVARCHAR(MAX)
    , CardText NVARCHAR(MAX)
    , CardAmber INT
    , CardPower INT
    , CardArmor INT
    , CardRarity NVARCHAR(MAX)
    , FlavorText NVARCHAR(MAX)
    , Expansion INT
    , IsMaverick BIT
    , CONSTRAINT UC\_dimCards\_KeyforgeID UNIQUE(CardKeyforgeID))"
    Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query

The dbatools PowerShell library is really powerful -- it can write objects straight to a table.  However, I have only been able to get it to work if I add another column to the beginning of the object.

    $CardTable = $cards | Select-Object @{N="FirstColumn";E={0}}, id, card\_number, card\_title, house, card\_type, front\_image, card\_text, amber, power, armor, rarity, flavor\_text, expansion, is_maverick
    Write-DbaDataTable -SqlInstance $server -Database $database -Table dimCards -InputObject ($CardTable)  -Verbose 

A single card can have multiple traits, so it will require a junction table to correctly account for everything.  This creates the dimTraits and factCardTraits tables:

    # Create dimTraits table
    Invoke-DbaQuery -ServerInstance $server -Database $database -Query "CREATE TABLE dimTraits (TraitID INT IDENTITY(1,1) PRIMARY KEY, TraitName VARCHAR(50) UNIQUE)"
    # Create factCardTraits table
    Invoke-DbaQuery -ServerInstance $server -Database $database -Query "CREATE TABLE factCardTraits (CardID INT, TraitID INT, CONSTRAINT UC_CardTrait UNIQUE(CardID, TraitID) )"

Next, I get an object that holds card id and traits for the card.  I loop through each card in the object and each trait for the card and insert into the two tables above.

\# Clean up traits (a card can have multiple traits)
    $TraitsXCards = $cards | Where-Object {$_.traits} | Select-Object id, traits
    foreach ($card in $TraitsXCards | Select-Object id, @{Name="Traits";Expression={$_.traits -split (' • ')}}){
        $KeyforgeCardID = $card.id
        $CardID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query "SELECT CardID FROM dimCards WHERE CardKeyforgeID = '$KeyforgeCardID'"
        foreach ($trait in $card.traits){
            $query = "select TraitID from dimTraits where TraitName = '$trait'"
            $TraitID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query
            if (-not $TraitID){
                $InsertQuery = "INSERT INTO dimTraits VALUES ('$trait')"
                Invoke-DbaQuery -SqlInstance $server -Database $database -Query $InsertQuery
                $TraitID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query
            }
            $query = "SELECT * from factCardTraits WHERE CardID = $($CardID.CardID) AND TraitID = $($TraitID.TraitID)"
            $Response = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query
            if (-not $Response){
                $query = "INSERT INTO factCardTraits VALUES ($($CardID.CardID), $($TraitID.TraitID))"
                Invoke-DbaQuery -ServerInstance $server -Database $database -Query $query
            }
        }
    }

The rest of the code is very similar to the above, but it loops through each individual deck to get the cards in the decks.  The first step is to create the dimDecks, factDeckHouses, and factDeckCards tables.  DimDecks will hold deck information such as name, factDeckHouses will link decks to their 3 houses, and factDeckCards will link decks to their 36 cards.

    #####################
    ##  Get Deck Data  ##
    #####################
    Invoke-DbaQuery -ServerInstance $server -Database $database -Query "CREATE TABLE dimDecks (DeckID INT IDENTITY(1,1) PRIMARY KEY, DeckKeyforgeID VARCHAR(255) NOT NULL, DeckName NVARCHAR(255), Expansion INT, PowerLevel INT, Chains INT, Wins INT, Losses INT, Notes NVARCHAR(MAX), CONSTRAINT UC_DeckKeyforgeID UNIQUE(DeckKeyforgeID))"
    Invoke-DbaQuery -ServerInstance $server -Database $database -Query "CREATE TABLE factDeckHouses (DeckID INT NOT NULL, House VARCHAR(255) NOT NULL, CONSTRAINT UC_DeckHouses UNIQUE(DeckID, House))"
    Invoke-DbaQuery -ServerInstance $server -Database $database -Query "CREATE TABLE factDeckCards (DeckID INT NOT NULL, CardID INT NOT NULL)" # originally had a unique constraint, but decks can have the same card multiple times

The next and final section loops through all decks on every page of the API and tracks the data on the page.  Future versions will likely only contain the meat of what is in this part of the script, as opposed to getting card information first. As discussed in the previous post, the first item in the API is called Count which holds the total number of decks available.  We set the number of decks per page in the $PageSize variable at the beginning of this script.  To calculate the total number of pages, it is just the count / page size:

    $totalPages = \[math\]::Ceiling($decks.count / $pagesize)

I then loop through all of those pages and get the same type of information we've gathered previously:

    foreach ($page in 1..$totalPages){
        Write-Host "On page $page of $totalPages"
        $url = "https://www.keyforgegame.com/api/decks/?links=cards&page_size=25&page=$page"

        $Response = Invoke-WebRequest $url -ContentType 'application/json; charset=utf8'
        $jsonCorrected = \[Text.Encoding\]::UTF8.GetString(
                  \[Text.Encoding\]::GetEncoding(28591).GetBytes($Response.Content)
                )
        $body = $jsonCorrected |ConvertFrom-Json

        $totalPages = \[math\]::Ceiling($body.count / $pagesize)


        $DeckCount = 0
        foreach($deck in $body.data){
            $DeckCount ++ 
            write-host "       Deck # $DeckCount of $pagesize"

            $query = "SELECT * FROM dimDecks WHERE deckkeyforgeid = '$($deck.id)'"
            $response = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query

            if (-not $Response){ # Deck hasn't been added

                $DeckTable = $deck | Select-Object @{N="FirstColumn";E={0}}, id, name, expansion, power_level, chains, wins, losses, notes
                Write-DbaDataTable -SqlInstance $server -Database $database -Table dimDecks -InputObject $DeckTable

                $DeckID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query "SELECT deckID FROM dimDecks WHERE DeckKeyforgeID = '$($deck.id)'"
                $DeckID = $DeckID.DeckID

                foreach ($house in $deck._links.houses){
                    Invoke-DbaQuery -SqlInstance $server -Database $database -Query "INSERT INTO factDeckHouses VALUES ($DeckID, '$house')"
                }

                foreach ($card in $deck._links.cards){
                    $CardID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query "SELECT cardID from dimCards WHERE CardKeyforgeID = '$card'"
                    $CardID = $CardID.CardID

                    if (-not $CardID){
                        Write-Host "Adding New Card to Database.  ID: $card"
                        $card = $body.\_linked.cards | Where-Object {$\_.id -eq $card}
                        $CardTable = $card | Select-Object @{N="FirstColumn";E={0}}, id, card\_number, card\_title, house, card\_type, front\_image, card\_text, amber, power, armor, rarity, flavor\_text, expansion, is_maverick
                        Write-DbaDataTable -SqlInstance $server -Database $database -Table dimCards -InputObject ($CardTable) 

                        $query = "SELECT cardID from dimCards WHERE CardKeyforgeID = '$($card.id)'"
                        $CardID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query
                        $CardID = $CardID.CardID

                        $TraitsXCards = $card | Where-Object {$_.traits} | Select-Object id, traits
                        foreach ($card in $TraitsXCards | Select-Object id, @{Name="Traits";Expression={$_.traits -split (' • ')}}){
                            $query = "select TraitID from dimTraits where TraitName = '$trait'"
                            $TraitID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query
                            if (-not $TraitID){
                                $InsertQuery = "INSERT INTO dimTraits VALUES ('$trait')"
                                Invoke-DbaQuery -SqlInstance $server -Database $database -Query $InsertQuery
                                $TraitID = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query
                            }
                            $query = "SELECT * from factCardTraits WHERE CardID = $CardID AND TraitID = $($TraitID.TraitID)"
                            $Response = Invoke-DbaQuery -SqlInstance $server -Database $database -Query $query
                            if (-not $Response){
                                $query = "INSERT INTO factCardTraits VALUES ($CardID, $($TraitID.TraitID))"
                                Invoke-DbaQuery -ServerInstance $server -Database $database -Query $query
                            }
                        }
                    }

                    #Write-Host "INSERT INTO factDeckCards VALUES ($DeckID, $CardID); $card"
                    Invoke-DbaQuery -SqlInstance $server -Database $database -Query "INSERT INTO factDeckCards VALUES ($DeckID, $CardID)"
                }
            }
        }

   

###  Next Steps

*   Database Updates
    *   Performance tune it (it's super slow right now!)
    *   Create Foreign Keys where appropriate
    *   Clean up some of the data that is messed up.  Specifically, the dimTraits table has some issues such as "Cyborg â?¢ Beast".  Break this into Cyborg and Beast.
*   Script Updates
    *   Create functions for the repetitive portions
    *   Use a queue to pull in all of the JSON via the API and split the work among multiple nodes to speed up the process
*   Project Updates
    *   Move the script to Azure
    *   Create a schedule to run regularly
    *   Create the API
    *   Create the PowerBI report

  _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._