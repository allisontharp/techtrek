---
title: 'Keyforge Data Scraping Part 3: Plots'
tags:
  - keyforge
  - powerbi
url: 1947.html
id: 1947
categories:
  - Personal Projects
  - Boardgames
  - Keyforge
date: 2018-12-07 12:00:39
---

In the previous two posts in this series, I looked at the [Keyforge API](http://www.techtrek.io/keyforge-data-scraping-part-1-the-api/) and wrote a [simple script to scrape it into my own database](http://www.techtrek.io/keyforge-data-scraping-part-2-the-script/).  In this post, I'll show some of the initial findings from this data.

Power BI
--------

First, I created a simple Power BI report by importing in all of my tables.  I checked the relationships that Power BI automatically created and left them as is for now: \[gallery size="medium" ids="1948"\] Ideally, this model would be a star schema.  I was too excited to dig into the data, so I left the model.  It would be a good idea to update the model to be closer to a star schema in the future.

Cards
-----

### The Rarest Rare (By Card Number)

The first question I was interested in was simple: which card is the rarest?  For this question, I am looking simply at _card number_.  This means that all mavericks are considered the same card for this analysis. As with most data related questions, this same question can be answered in multiple ways.  Because one deck can have multiple copies of the same card, rare could mean which card has the _fewest occurrences_ or it could mean which card _exists in the fewest decks_.  It is a simple distinction and may or may not have an impact.  Let's see! In PowerBI, I created a Slicer to filter the page by Card Rarity (Common, Uncommon, Rare, Fixed, and Variant).  Then, I created a simple table that had Card Name with the Count of CardID (the number of times a specific card name exists across all decks) and Count of DeckID (the number of decks that a card name appears in) fields.  The results for Rare card types are below: ![](http://www.techtrek.io/wp-content/uploads/2018/12/Screenshot-2018-12-03-at-10.20.57-PM.png) From this, we can see that the rarest rare is Master of 1 (it has the fewest occurrences as well as the fewest decks).  However, a more interesting question emerged when looking at the data in this way.  Over the first 200,000 registered decks, not a single one has multiple copies of Master of 1.  We know this to be true because the Count of CardID is the same as the Count of DeckID.  This lead  to my next question:

### What Cards Are Always Unique in a Deck?

To answer this, I created a measure called Dif with the following calculation:

Dif = count(factDeckCards\[CardID\]) - DISTINCTCOUNT(factDeckCards\[DeckID\])

In other words, Dif = Count of Card ID - Count of Deck ID.  I also added a column for the card's main house (not including mavericks). ![](http://www.techtrek.io/wp-content/uploads/2018/12/Screenshot-2018-12-03-at-10.29.50-PM.png) The results are not terribly surprising.  There are 14 cards that seem to never get duplicated in a single deck:

*   Bait and Switch
*   Burn the Stockpile
*   Chota Hazri
*   Ether Spider
*   Key Charge
*   Key Hammer
*   Lash of Broken Dreams
*   Library Access
*   Master of 1, 2, and 3
*   Replicator
*   Restringuntus
*   Wardrummer

### The Rarest Rare (Including Mavericks)

There currently 307 cards that exist in only one deck, all of which are mavericks.

Decks
-----

### Which decks have the most cards in common?

This is a question I've been a little obsessed with over the last week.  I've run queries to make sure no two decks have the same 36 cards and also checked to see if any decks have the same _distinct_ cards (as in, the same cards but potentially different numbers of each card).  So far, there are none -- even when you consider all mavericks the same card!  This got me wondering -- which two decks have the most cards in common? It turns out, this is kind of a hard problem to solve at a large scale.  I tried several queries to try to solve it in a set based way and always ran out of memory or had other errors.  Ultimately, I decided to brute force the solution by looping through each deck and comparing it to each other deck (gross, I know).  I'm not proud of this, but the below query ran for 2 days and 20 hours for 206,000 decks:

select fdc.deckid
	,	dc.CardNumber
	,	count(*) as cntOccurrences
into	#DeckCardNumbers
from factDeckCards as fdc
join dimcards as dc on dc.CardID = fdc.cardid
group by fdc.deckid, dc.CardNumber


/*
	Duration: 2.20:34:06
	Records: 206750
*/

declare @name int
DECLARE db_cursor CURSOR FOR 
select deckid
from DecksProcessed as dp
where dp.isProcessed =0

OPEN db_cursor  
FETCH NEXT FROM db_cursor INTO @name  

WHILE @@FETCH_STATUS = 0  
BEGIN  
   insert into deckComparison
      select 
		d1.deckid as Deck1
	,	d2.deckid as Deck2
	,	count(*) as cntCardsInCommon
from #DeckCardNumbers	as	d1
join #DeckCardNumbers	as	d2
	on	d2.deckid > d1.deckid
	and	d2.CardNumber = d1.CardNumber
where	d1.deckid = @name
group by d1.deckid, d2.deckid
having count(*) >= 18

update d
set isProcessed=1
from decksProcessed as d
where d.deckid=@name

print @name
      FETCH NEXT FROM db_cursor INTO @name 
END 

CLOSE db_cursor  
DEALLOCATE db_cursor

This is a simple cursor that loops through each card in each deck and compares it to each other deck.  I did help myself by limiting to DeckID2 > DeckID1.  This means that we will compare deck 1 to decks 2 and 3, but decks 2 and 3 don't compare to deck 1 because we already have that comparison. The results were interesting!  The most any unique cards any two decks have in common is 23.  There are 4 pairs of decks that share 23 cards in common: ![](http://www.techtrek.io/wp-content/uploads/2018/12/Screenshot-2018-12-04-at-5.37.40-PM.png)

### Which decks have the most rares?

The most rares in a single deck is 11.  Three decks have 11 rares: The Advisor that Magnetizes Bones Umberto the Gratuitously Learned Edgeraven, Prison Bandit

### Which deck has the most of a single card?

Six is the highest number of occurrences of a single card number in a deck.  There are 12 decks where this has happened and they all had it happen with Niffle Ape.

### Which deck has the most horsemen?

Recently, a double horseman deck sold on [eBay for over $2,000](https://www.ebay.com/itm/double-horseman-deck-Keyforge-FFG-UDG/283264151117?hash=item41f3dbee4d:g:V6AAAOSwUBtb72xf).  Did this deck have the most horsemen or are there others that have more?  What percentage of decks even have double horsemen? No decks currently have more than 2 horsemen.  There are 116 decks that have double horseman (out of 21,372 decks with at least one horseman).

### What are the chances of having 4 horseman decks out of 55 decks?

I went to a local sealed deck Keyforge tournament this week.  In a sealed tournament, participants use decks that are purchased at the event.  You have a certain amount of time (in this case, 20 minutes) to review the deck before the tournament actually begins.  In one of the rounds, I lost to a person who received a horseman deck.  Afterwards, I was checking out his deck and commented that I hadn't yet seen or played against the horsemen.  He told me he has bought 55 decks, four of which have horsemen.  What are the odds? At the time of this writing, there are 21,372 decks out of 245,799 horseman decks, meaning there is about an 8.69% that any given deck will be a horsemen deck.  To calculate the chances of getting 4 horsemen decks out of 55 that you've bought, it would be: (55 choose 4) * (0.0869^4) * (0.913 ^ 51) = 19% chance, not as unlikely as I had originally thought!

###### _Note: I haven't taken a stats class in over 10 years.  Don't go gambling on my faulty memory of probability calculations._

### How often are decks registered?

Starting on 12/5/2018 at 22:43, I started tracking the number of decks registered every 5 minutes.  This chart will get more interesting over time, but here are total number of decks as well as number of decks registered during the 5 minute period: ![](http://www.techtrek.io/wp-content/uploads/2018/12/Screenshot-2018-12-07-at-3.03.39-PM.png)

Next Steps
----------

*   Database Updates
    *   Performance tune it (it’s super slow right now!)
    *   Create Foreign Keys where appropriate
    *   Clean up some of the data that is messed up.  Specifically, the dimTraits table has some issues such as “Cyborg â?¢ Beast”.  Break this into Cyborg and Beast.
*   Script Updates
    *   Create functions for the repetitive portions
    *   Use a queue to pull in all of the JSON via the API and split the work among multiple nodes to speed up the process
*   Project Updates
    *   Move the script to Azure
    *   Create a schedule to run regularly
    *   Create the API

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._