---
title: Checking Board Games In/Out With A USB Scanner — Part 1
tags:
  - boardgames
  - Python
url: 1542.html
id: 1542
categories:
  - Personal Projects
  - Boardgames
date: 2016-07-15 08:00:22
---

I am working on a project that will allow me to use the existing barcodes on board games to catalog them into a personal database of games that I own.  You can read up on that project [here](http://www.techtrek.io/inventorying-board-games-with-a-usb-scanner-part-1-overview/). That project has more or less wrapped up.  The next step is a related but different project: creating a check in/out system.  This system will be closely tied to our inventory system, so it will be in the same database. I run an annual board game themed fundraiser called [Play It Forward](http://www.playitforwardfundraiser.com).  This year, we are going to try to have a system that will allow players to check out games from our library.  Each participant at the event will have a badge with a unique barcode.  When they want a game, a librarian will first scan their barcode and then the barcode of the game they want.  This will update the game in the database as being checked out.  When the person is done with the game, they will return it to the librarian who will then scan the barcode of the game.  The database will then check the game in.

### The Schema

Again, the schema for this project is pretty easy.  I'm building this within the inventory database, so the _inventory _table shown below already exists (except I added the _num_checkedout_ column).  The relevant schema is: ![checking (6)](http://www.techtrek.io/wp-content/uploads/2016/07/checking-6.png) A player can have multiple games checked out.  Because we have multiple copies of some games, some games could potentially be checked out by multiple people.  This means the inventory and players tables have a many-to-many relationship. Also, since we own multiple copies of some games, we can't simply have a boolean (yes/no) column for each game for being checked out or checked in.  When a game gets checked out, the _num_checkedout_ column will increment.  Within the Python script, we are going to have to make sure the _num_checkedout_ does not exceed the number in the _owned_ column.

### The Code

I added three functions: _checkin_, _checkout_, _checking_, and _numcheckedout_.  The checkin function checks a game in to a specific player, the checkout function checks the game out to a function, and the checking function checks to see if the game should be checked out or checked in.  Finally, the numcheckedout function checks to see how many copies of a specific game are checked out.  The functions are below:

def checking(c, upc):
        global menu_select
        
        if upc == "":
                True
        if upc\[0\] == '/':
                commands(upc)
        else:
                # search UPC in inventory table.
                bggid = upc_search(upc)
                if bggid: # game is in inventory.
                        bggid = bggid\[0\]
                
                        upc = raw_input("Scan Player Badge: ")
                        playerid = player_search(upc)
                        if playerid: # player is in the player table
                                # check to see if the player has that game checked out
                                print playerid
                                print bggid
                                query = "SELECT checkingid FROM checking WHERE checkin = 0 AND playerid = {pi} AND bggid = {bi}".format(pi = playerid, bi = bggid)
                                print query
                                c.execute(query)
                                
                                checkingid = c.fetchone()
                                if checkingid: # the user has that game checked out
                                        checkin(bggid, playerid, checkingid\[0\])
                                else: # that user doesn't have that game checked out
                                        checkout(bggid, playerid)
                else:
                        playerid = player_search(upc)
                        if playerid:
                                upc = raw_input('Scan Board Game: ')
                                bggid = upc_search(upc)
                                if bggid:
                                        bggid = bggid\[0\]
                                        query = "SELECT checkingid FROM checking WHERE checkin = 0 AND playerid = {pi} AND bggid = {bi}".format(pi = playerid, bi = bggid)
                                        c.execute(query)
                                        checkingid = c.fetchone()
                                        if checkingid: # the user has that game checked out
                                                checkingid = checkingid\[0\]
                                                checkin(bggid, playerid, checkingid)
                                        else: # that user doesn't have that game checked out
                                                checkout(bggid, playerid)
                                        
                
                        
                        # to do: update checking tabple to check in the game there, too
                        # to do: check to make sure user doesn't already have the game checked out before checking in
                        # to do: update checking table to check the game out there too
                                
                                
                                
                
                
                
                
                        
        conn.commit()

def checkin(bggid, playerid, checkingid):
        # to do: make sure the game is checked out before checking it in
        num = numcheckedout(bggid)
        
        
        
        query = "UPDATE checking SET checkin = now() WHERE checkingid = {ci}".format(ci = checkingid)
        c.execute(query)
        

        query = "UPDATE inventory SET numcheckedout = {num} WHERE bggid = {bggid}".format(num = int(num)-1, bggid = bggid)
        c.execute(query)
        
        conn.commit()
        
        gamename = get_name(bggid)
        
        print "Checked in {gamename}".format(gamename = gamename)
        
def checkout(bggid, playerid):
        # to do: make sure player doesn'thave a game checked out already (select checkingid where checkin = 0 and playerid =)
        # to do: add override command to allow a player to have multiple games checked out

        num = numcheckedout(bggid)
        
        query = "UPDATE inventory SET numcheckedout = {num} WHERE bggid = {bggid}".format(num = int(num)+1, bggid = bggid)
        c.execute(query)
        
        query = "INSERT INTO checking (checkout, playerid, bggid) VALUES (NOW(), {pi}, {bi})".format(pi = playerid, bi = bggid)
        c.execute(query)
        
        gamename = get_name(bggid)
        
        print "Checked out {name} to player".format(name = gamename)
        
        conn.commit()
        
def numcheckedout(bggid):
        query = "SELECT numcheckedout FROM inventory WHERE bggid = {bi}".format(bi = bggid)
        c.execute(query)
        numcheckedout = c.fetchone()\[0\]
        
        return numcheckedout

There is still quite a bit I'd like to do to make this better.  Most of these are noted in the "TO DO" comments in the code above.  However, the script is functioning as is!

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._