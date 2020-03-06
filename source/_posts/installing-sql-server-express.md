---
title: Installing SQL Server Express
tags:
  - SQL Server
url: 771.html
id: 771
categories:
  # - How To
date: 2016-02-26 08:00:49
---

SQL Server Express is a free version of Microsoft's SQL based database system.  According to the [Microsoft website](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/sql-server-express.aspx), it "includes 10GB of storage per database, \[is\] easy backup and restore to Microsoft Azure functionality, and \[is compatible\] with all editions of SQL Server and Microsoft Azure SQL Database so you can develop and deploy with confidence." Since I'm ultimately interested in obtaining a [Microsoft Technology Associate in IT Infrastructure and/or a Microsoft Certified Solutions Associate for Windows Server](https://www.microsoft.com/en-us/learning/windows-server-certification.aspx), I figure SQL Server Express will be a good place to start getting a feel for SQL Server.

### Downloading SQL Server 2014 Express

To download SQL Server 2014 Express, click the download link [here](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/sql-server-express.aspx). ![2016-02-24_17h06_00](/wp-content/uploads/2016/02/2016-02-24_17h06_00.png) Microsoft will ask you to log in with your Microsoft account.  If you have a Windows 10 machine, you should already have an account.  If not, you can create one on that screen. You'll be taken to a survey where it asks which version of SQL Server 2014 Express you'd like to download.  There are **many** options to choose from and I honestly wasn't sure which to pick.  SQL Server 2014 Express with Tools seemed like perhaps the best option for me, so that is what I went with. ![2016-02-24_17h10_38](/wp-content/uploads/2016/02/2016-02-24_17h10_38.png) After answering the rest of the required questions, click continue.  An executable will begin to download.  It took quite awhile for mine to download, but your mileage may vary. Once the .exe is downloaded, go ahead and run it.  Once the Installation Center opens, it will ask if you want to set up a new SQL Server stand-alone installation or upgrade.  I don't have a current SQL Server instance running, so I chose the first option. I went through the installation process and chose all of the default settings.  To test it out, I opened the software by pressing the windows key and typing 'SQL Server'.  Open 'Microsoft SQL Server Management Studio'.  A box should pop up that looks similar to this: ![2016-02-24_23h10_30](/wp-content/uploads/2016/02/2016-02-24_23h10_30.png) Press 'Connect' to connect! This is my first time ever using SQL Server, so I'm excited to get into it and see what it has to offer.  Initially, it seems quite a bit different and more complex than both SQLite and MySQL. If you have any questions or suggestions, feel free to let me know in the comments or [contact me](/contact/)!