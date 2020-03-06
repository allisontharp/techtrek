---
title: An Introduction To PowerShell
tags:
  - Automation
  - powershell
url: 1576.html
id: 1576
categories:
  - How To
date: 2016-08-16 08:00:55
---

I first heard about PowerShell about a year ago.  Over the last few months, the topic has come up more and more.  It seems like a great tool to add to my toolbox for automating tasks within Windows, so I thought now would be a good time to check it out.

### What is PowerShell?

PowerShell is developed by Microsoft, specifically for task automation and configuration management.  A _shell_ is a user interface that gives the user access to services of an operating system.  The shell you are likely most familiar with is the Command Prompt.  PowerShell is like the Command Prompt in many ways, but it is much more powerful. PowerShell is based on the .Net framework and is considered a scripting language.  Windows also developed the Windows PowerShell ISE (Integrated Scripting Environment).  This is a GUI that lets us make scripts without having to type all of the commands into the command line.

### Why Use PowerShell?

At its core, PowerShell can help us things like repetitive tasks, processing or acting on many files at once, automating and scheduling tasks, and configuring components/services.  For example, you might use PowerShell to display all of the USB devices installed on every computer on the network.

### Basics

A **cmdlet** (pronounced command-let) is a special type of function within PowerShell.  Some common cmdlets are:

*   get-command: shows all commands available within PowerShell (including aliases, functions, and cmdlets)
*   get-help: shows help documentation on various topics
*   get-location: gets the current directory
*   set-location: change the current directory
*   copy-item: copies files
*   remove-item: remove a file/directory
*   move-item: move a file
*   rename-item: rename a file
*   new-item: create a new (empty) file/directory

### Open PowerShell

Microsoft has included PowerShell within Windows since Windows 7.  To open it, type the windows key and type "powershell".  This opens the Windows PowerShell.  You'll notice right away that it looks very similar to the command prompt.![Capture](/wp-content/uploads/2016/07/Capture-9.jpg)   In fact, you can do anything within PowerShell that you can do within the command prompt.  This means if I want to open up Python, I can just type 'python'; if I want to change directories, I use the 'cd' command, etc.

### Open PowerShell ISE

To open the ISE, press the Windows key and search 'PowerShell ISE'.  Once open, it looks like this: ![Capture](/wp-content/uploads/2016/08/Capture-1024x542.jpg) To get a feel for how PowerShell works, lets try some of the cmdlets specified in the **Basics** section above. Type

get-command

Once complete (may take a couple of seconds), you'll see a list of all of the commands.  The first few lines look like this: ![Capture](/wp-content/uploads/2016/08/Capture-1.jpg) I am new to PowerShell, but I think the get-help command will be one of my most used commands in the beginning.  To get help on everything, type:

get-help *

This isn't particularly useful.  We will usually want to get help on a specific function.  To get help on the get-command cmldlet, type:

get-help get-command

![Capture](/wp-content/uploads/2016/08/Capture-2-1024x264.jpg) This shows us the general syntax, the aliases, and remarks but it doesn't give us a lot of detail.  To get more information on this function, we can type:

get-help get-command -detailed

![Capture](/wp-content/uploads/2016/08/Capture-3-1024x665.jpg) This is definitely better because it allows us to see the parameters more easily. In PowerShell, the asterisk (*) is used as a wildcard.  Say you can't remember the name of a command, but you know it was something-object.  We can search for it using the **Get-Command** cmdlet like this:

get-command *-object

This will list all of the commands that end in '-object'. That is all for this first introduction post.  The next step for me is to come up with a real world project to really try it out.   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._