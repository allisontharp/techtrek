---
title: 'Keyforge Data Scraping Part 1: The API'
tags:
  - keyforge
url: 1909.html
id: 1909
categories:
  - Personal Projects
  - Boardgames
  - Keyforge
date: 2018-11-21 09:23:20
---

[Keyforge](https://www.keyforgegame.com/) is a new card game by the person that designed [Magic: The Gathering](https://boardgamegeek.com/boardgamedesigner/14/richard-garfield).  The interesting aspect of this game is that the decks are all procedurally generated.  There is a pool of about 350 cards in the game and each deck has a unique grouping of 36 cards.  The developers use an algorithm to try to balance the decks and to also make sure that each deck is playable.  In addition to each deck having a unique set of cards, the decks also have unique names and unique art on the back. When you purchase a deck, you can scan a QR code on one of the cards to register the deck.  This allows others to see the cards in your deck through the [Keyforge website](https://www.keyforgegame.com/search) or app.  A really cool additional feature of the website is that it has an API, which allows you to be able to get data on all of the decks and all of the cards.  I would not have been able to figure this out without the initial work done by BatmanAMA on [GitHub](https://github.com/keyforg/keyforge-cards-json).  His PowerShell script set me in the right direction for where the API is located and how to use it. I wrote a PowerShell script to scrape the API data and store it in a SQL Server Database.  In this series, I'll document the API, the PowerShell script, the database design, and some fun observations with the data.

API Overview
------------

The base URL for the API is

https://www.keyforgegame.com/api/decks/?page=1&links=cards

This will return a JSON object.  If you're not aware, FireFox has a great built in tool that attempts to format JSON objects in a human readable way.  If you are interested in trying out the above URL, use FireFox! The API has three main objects: Count, Data, and _linked.

#### Count

This is a simple key-value pair that returns the number of decks currently in the database.

#### Data

Returns an array of deck objects.  Each object represents one deck and has the following parameters:

*   id (the unique identifier for the deck)
*   Name (the deck name)
*   Expansion (Right now, there is only one expansion, and it has an ID of 341)
*   Power_Level: Coming soon in the web interface.  Until then, this is always 0.
*   Chains: Coming soon in the web interface.  Until then, this is always 0.
*   Wins: Coming soon in the web interface.  Until then, this is always 0.
*   Losses: Coming soon in the web interface.  Until then, this is always 0.
*   is\_my\_deck: false if you are not logged in or if you do not have the deck set as yours.
*   notes: Users can add notes to the decks.  If there are no notes, this will be empty (\[\])
*   is\_my\_favorite: false if you are not logged in or if you do not have the deck set as a favorite.

In addition to the key value pairs listed above, there is also an object of arrays named _links.  This object holds:

*   Houses: the three houses that the deck belongs to
*   Cards: an array of the 36 card IDs that make up the deck

#### _linked

This object has an array of arrays:

*   houses
    *   Array of all of the houses for all of the decks mentioned in the Data section above.  Each object in the array has:
        *   ID: name of the house
        *   Name: name of the house (same as ID)
        *   Image: URL of the image
*   cards
    *   Array of all of the cards from the data section above.  The data section only has the card ids, this array has the card details.
    *   Each card object has the following:
        *   id: Unique identifier, this is how you would link the information from the data above
        *   card_title: Name of the card
        *   House: Card House
        *   Card_Type: Such as creature, action, etc
        *   Front_Image: URL of the front image
        *   Card_Text: Text which describes what the card does
        *   Traits: Can be one or multiple.  Such as Martian or Soldier
        *   Amber: Number of amber that the card provides, if any (otherwise 0)
        *   Power: Power level of the card, if any (otherwise 0)
        *   Armor: Armor on the card, if any (otherwise 0)
        *   Rarity: Common, Uncommon, Rare, Special
        *   Flavor_Text: null if none
        *   Card_Number: the number of the card
        *   Expansion: Currently there is only one option for this (341)
        *   Is_Maverick: This is a boolean field for if the card is a maverick

  This appears to be just the API for the decks and is the only API end point I was able to find.  It is possible there are other API endpoints for Keyforge that I am not aware of. In [the next post](/keyforge-data-scraping-part-2-the-script/), we will review the PowerShell script I used to scrape all of the data using this API. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._