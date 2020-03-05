---
title: 'Gunsmith Db and App Part 3.1: Acquisition Flow'
tags:
  - gunsmith db
  - MySQL
url: 468.html
id: 468
categories:
  - Personal Projects
  - GunsmithDb
date: 2016-01-12 08:00:11
---

In [Part 2](http://www.techtrek.io/index.php/2016/01/01/gunsmith-db-and-app-part-2-adding-customers/) of this series, I discussed how I added the ability for a user to create a new customer in the database.  In this part, we will discuss how a user will track an acquisition and how I set it up in VB and MySQL. If you'd like to follow along, you can install and run the software [here](http://www.techtrek.io/wp-content/uploads/2016/01/publish-2.htm). The Acquisition tab is shown below.  Note: the GUI has gone through a few design iterations since the last post, so it looks a little different! ![2016-01-11_06h32_31](http://www.techtrek.io/wp-content/uploads/2016/01/2016-01-11_06h32_31.png)

### User Flow

##### Search For or Create A New Customer

The first thing a user will do is search for an existing customer or create a new customer.  The user can search for a customer by filling out one or more of the textboxes within the Customer group.  Additionally, if they are not sure how to spell something, the user can use partial words. For instance, if a user were to put the letter 'c' in for first name, all of the customers with the letter 'c' in their first name would appear: ![2016-01-11_06h36_29](http://www.techtrek.io/wp-content/uploads/2016/01/2016-01-11_06h36_29.png) A user can partially or fully fill out more than one textbox as well.  A user can select a customer by either double clicking on the customer's name or by right clicking and selecting 'Select Customer'. ![2016-01-11_18h29_58](http://www.techtrek.io/wp-content/uploads/2016/01/2016-01-11_18h29_58.png) If the customer is new, the user must completely fill out **all** of the textboxes and then click 'Add'.  If a required field is missing, the user is notified in the notification box on the bottom right. ![2016-01-11_18h22_19](http://www.techtrek.io/wp-content/uploads/2016/01/2016-01-11_18h22_19.png) Once the user is successfully added, the notification box will alert the user. ![2016-01-11_18h26_00](http://www.techtrek.io/wp-content/uploads/2016/01/2016-01-11_18h26_00.png) After a customer is created, it will automatically be selected.

##### Search For a Firearm

Next, the user must search for and select the firearm that is involved in the transaction.  For my friend, all firearms will fall into one of four categories: gunsmithing, retail, manufactured, or transfer.  He must select one of those categories then then select the caliber, manufacturer, model, manufacturer country, and type of the firearm.  For any of these, with the exception of type, he can also add an entry into the database.  For instance, if the specific model of firearm is not currently in the database, he can select 'New' next to the 'Model' dropdown to add the model to the database. ![2016-01-11_18h35_07](http://www.techtrek.io/wp-content/uploads/2016/01/2016-01-11_18h35_07.png) The user must also fill in when he acquired the firearm and when the transaction took place.  Usually both of these will be the current date, so the default date for both of these is always today. Once all of the required items have been filled in, the user can press submit.  Again, if any required information is missing, it will be noted in the notification box.  Otherwise, the notification box will say 'Submitted'.![2016-01-11_18h38_40](http://www.techtrek.io/wp-content/uploads/2016/01/2016-01-11_18h38_40.png) In this [next part](http://www.techtrek.io/gunsmith-db-and-app-part-3-2-acquisition-code/), we discuss how the coding behind this portion of the GUI.