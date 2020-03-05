---
title: Installing and Using MongoDB with Linux
tags:
  - Linux
  - MongoDB
  - NoSQL
  - Ubuntu
url: 1027.html
id: 1027
categories:
  # - How To
date: 2016-04-01 08:00:50
---

I think that in order to take the next big step in my Tech Trek journey, I really need to get a better understanding of Linux.  I'm going to try to start doing more and more things in Linux so that I can get comfortable with it.  This is one of the first steps towards that goal. MongoDB is a NoSQL database solution.  If you are new to [NoSQL](http://www.techtrek.io/what-is-nosql/) or [MongoDB](http://www.techtrek.io/an-introduction-to-mongodb/), check out my posts from the last couple of weeks regarding those topics.  For this tutorial, I will assume you have an Ubuntu machine up and running and can get to the command prompt. I am using Ubuntu 14.04.3 and I will be following the [Official MongoDB guide](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwj1xdTQnezLAhXhvoMKHZ-CC1EQFggcMAA&url=http%3A%2F%2Fdocs.mongodb.org%2Fmanual%2Ftutorial%2Finstall-mongodb-on-ubuntu&usg=AFQjCNEspWLjlpAbzxof28hoCmR1Wy2aeQ&sig2=7y0YYvMYbTuIU2dLRErRCw) for installing MongoDB on Ubuntu.

### Backing Up Your Machine

I have some MySQL databases on my Linux machine and Tech Trek runs off of it.  Since I'm new to Linux, I want to try to back up as much stuff as possible just in case.  My motto for all things in life is _better safe than sorry_. First, I want to back up the MySQL databases (since I already know how to do that).  Accessing and manipulating MySQL is the same within Linux as it is in Windows.  To run Mysql, type \[text\] mysql -u username -p \[/text\] where _username_ is your username.  You will be prompted for your password, go ahead and type it in (it will look like you aren't typing anything -- this is how it is supposed to be!  Just press Enter once you have typed the password).  From here, MySQL works exactly the same.  To see our databases, type: \[text\] show databases; \[/text\] I see that I have 4: information\_schema, forensicswordpress, mysql, and performance\_schema.  I assume forensicswordpress is the database that has all of Tech Trek's info, but I'm not sure what the others are.  I'll back them all up just in case. To back up these databases, I am going to create a MySQL dump and then move the .sql files over to my local machine.  If you are unsure how to do this, you can follow my guide [here](http://www.techtrek.io/moving-your-mysql-database-to-another-location/). To backup my Wordpress site, I log in as Admin and go to Tools -> Export.

### Installing MongoDB on Ubuntu

##### Import the public key used by the package management tool

Issue the following command to import the [MongoDB public GPG Key](https://www.mongodb.org/static/pgp/server-3.2.asc?_ga=1.70969808.1367410909.1457828693): \[text\] sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927 \[/text\]   You should receive an output that is similar to this: \[text\] Executing: gpg --ignore-time-conflict --no-options --no-default-keyring --homedir /tmp/tmp.EkL4HGeuJ8 --no-auto-check-trustdb --trust-model always --keyring /etc/apt/trusted.gpg --primary-keyring /etc/apt/trusted.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927 gpg: requesting key EA312927 from hkp server keyserver.ubuntu.com gpg: key EA312927: public key "MongoDB 3.2 Release Signing Key <packaging@mongodb.com>" imported gpg: Total number processed: 1 gpg: imported: 1 (RSA: 1) \[/text\]

##### Create a list file for MongoDB

We need to create the /etc/apt/sources.list.d/mongodb-org-3.2.list list file.  I'm using Ubuntu 14.04 so my command is: \[text\] echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list \[/text\] This line should output something like: \[text\] deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse \[/text\]

##### Reload local package database

To reload the database, use the command: \[text\] sudo apt-get update \[/text\] You'll have a long output that will end in: \[text\] Fetched 4,311 kB in 7s (581 kB/s) Reading package lists... Done \[/text\]

##### Install the MongoDB packages

Now onto the good stuff!  I want to install the latest stable version of MongoDB so I'll use the command: \[text\] sudo apt-get install -y mongodb-org \[/text\] The _sudo apt-get install_ part of this installs the application _mongodb-org_.  The qualifier _-y_ tells the installer to assume 'Yes' for any questions it might ask.  I think the anagolus installation method in Windows would be doing a 'Recommended' install vs a 'Custom' install.

### Running MongoDB on Ubuntu

##### Start MongoDB

Okay, lets start it up!  Use the command: \[text\] sudo service mongod start \[/text\] Remember from my [post on installing MongoDB on Windows](http://www.techtrek.io/an-introduction-to-mongodb/), the install directory had many executible files.  The two we cared about were _mongod.exe _(the server) and _mongo.exe_ (the shell).  This command is the same as running \[text\] mongod \[/text\] on a Windows machine (assuming you set your Environment Variable). We can confirm that MongoDB started successfully by checking the log file.  This file is located at **/var/log/mongodb/mongod.log**.  To read it, we will use [Vim](http://www.vim.org/).  Vim is a powerful text editor that is built into Ubuntu.  It takes a little while to get used to because you use your keyboard to navigate the text document.  There are a lot of resources online for getting started with Vim.  I will show you here just enough to check if MongoDB is running.  Open the file in Vim by typing: \[text\] vim /var/log/mongodb/mongod.log \[/text\] When you press Enter, it will open the text document up in Vim.  You will be at the top of the document.  To get to the end of the document, type 'G'.  You should see something that looks like: \[text\] Time  \[initandlisten\] Initializing full-time diagnostic data capture with directory '/var/lib/mongodb/diagnostic.data' \[/text\] To close the document, type: \[text\] :q! \[/text\]

##### Open Mongo Shell

To open the Mongo shell, simply type: \[text\] mongo \[/text\] From here, we can create collections and documents just like we did in Windows!

##### Stop MongoDB

When you are done, you can stop the server with: \[text\] sudo service mongod stop \[/text\]   That's it!  This was much easier than I imagined it would be.  I'm excited to start using Linux more and getting more comfortable with NoSQL so this exercise was really good for me. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._