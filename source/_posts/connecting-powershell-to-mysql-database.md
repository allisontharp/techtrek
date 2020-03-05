---
title: Connecting PowerShell to MySQL Database
tags:
  - MySQL
  - powershell
url: 1704.html
id: 1704
categories:
  - How To
date: 2016-09-13 08:00:43
---

I'm trying to learn more about PowerShell because I think it is a tool that needs to be in my toolbox.  To that end, I am going to try to redo parts of some of the projects I've already discussed on the blog.  The first step for many of these projects will be the same: connecting PowerShell to the MySQL database.  Here are the steps I used to do that:

#### 1\. Download Connector/Net

The first step is to download the ADO.Net MySQL driver, which can be found [here](http://dev.mysql.com/downloads/connector/net/).  Note the directory where this will be installed.

#### 2\. Load the .dll in PowerShell

Open the PowerShell ISE.  Our first line will be:

\[void\]\[System.Reflection.Assembly\]::LoadFrom("E:\\Program Files (x86)\\MySQL\\Connector.NET 6.9\\Assemblies\\RT\\MySql.Data.RT.dll")

Note, the directory for your MySql.Data.RT.dll might be different (likely just on the C: drive instead of the E: drive). PowerShell uses square brackets (\[\]) for two things: array index ($myvariable\[0\]) and to denote datatypes.  The first use is pretty common among many other languages (such as Python).  In this case, the first set (\[void\]) is casting the result to a void type.  An easier way to understand this might be with the following example:

\[int\]$myint = read-host "Enter an integer"

Here, we are limiting the $myint variable to only be an integer (not a float, string, void, etc).  In our own example, we are saying that the result of

\[System.Reflection.Assembly\]::LoadFrom("E:\\Program Files (x86)\\MySQL\\Connector.NET 6.9\\Assemblies\\RT\\MySql.Data.RT.dll")

must be \[void\].  This is because the above command would otherwise result in some success indication, which we don't want to be bothered with in our output.  The \[void\] casting suppresses the function's output, so that the result window will ignore it. The double colon (::) calls a static method of a .NET class.  If you are familiar with Python, this is similar to Python's syntax of Module.Method.

#### 3\. Define and Open the Connection String

Next, we need to create a new variable called _$myconnection_.  This variable will be a MySqlConnection object.  We are able to call this object because we previously loaded the MySQL .dll file:

$myconnection = New-Object MySql.Data.MySqlClient.MySqlConnection

Next, we'll define our connection string.  In general, the string will look like this:

$myconnection.ConnectionString = "server=yoursever;user id=youruser;password=yourpassword;database=yourdb;pooling=false"

If the MySQL database is on your local machine, the server will be _localhost_.  Otherwise, your sever will be the IP of the server your database is on.  The user id and password variables are the mysql username and password, not the server username/password.  If this does not work, it is likely because the user you defined in the connection string does not have access to the database on the machine you are on.  To fix this, you can run the following command in MySQL:

GRANT ALL PRIVILEGES ON *.* TO 'youruser'@'%' IDENTIFIED BY 'yourpassword' WITH GRANT OPTION;

Next, open the connection:

$myconnection.Open()

### 4\. Send Command and Read Output

Finally, we'll send a command and display the result in the output window.  To do this, use the commands:

$mycommand = New-Object MySql.Data.MySqlClient.MySqlCommand
$mycommand.Connection = $myconnection
$mycommand.CommandText = "SHOW TABLES"
$myreader = $mycommand.ExecuteReader()
while($myreader.Read()){ $myreader.GetString(0) }
$myconnection.Close()

This is pretty straight forward.  We first create the $mycommand variable, link it to our connection, and define our command text.  We then create a new variable, $myreader, which executes the reader and we send each line of the output to the results screen.  Finally, we close out our connection. That's it!  This is a good step for many other projects, so I'm excited to have a place to go from here. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._