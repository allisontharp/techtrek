---
title: An Introduction to Git
tags:
  - git
url: 1761.html
id: 1761
categories:
  - How To
date: 2016-09-30 08:00:40
---

It's been pointed out to me that I do a lot of Introduction posts.  I am nearing my 1 year mark on the blog, so I am hoping to start getting deeper into some of the topics I have covered previously (seems like it is about time!).  However, there is still one topic that is really important that I don't know well enough yet: git.

### What Is Git?

Git is a version control system (VCS), which is just what it sounds like: a system to help keep track of different versions of software.  Git isn't the only VCS out there (others include CVS, SVN, and Fossil), but it is one of the more popular systems, particularly for open source projects.  You've certainly used software that was developed using Git (Firefox and Chrome are two big ones!). Version control is really helpful when you are working with other people.  Without version control, if I send you a file I'm working on and you make changes to it, we would suddenly have two versions.  If I integrate your changes into my file, then we'd only have one file but no history!  Even when working alone, version control is really helpful for us to keep track of how the project is moving along.

### What Are Snapshots?

Git works with _snapshots_.  Every time we _commit_ (which just means saving the project), Git 'takes a picture' of what all of the files look like in your project in that moment and stores a reference to that snapshot.  If some of the files were unchanged, it won't store that file again but rather link back to the most recent version of that file that was changed.  If we are working collaboratively and you make a change to some of the files in our project, we can then merge your changes into our _branch_ (this just means we are branching away from the main line of development to continue working on something without messing with the main line).  If we decide later on that the path we were on was not something we should have been doing, we can always rollback to our last good version.

### Work Flow

In general, there are three states that your files will reside in:

*   Committed - The data is safely stored in your local database
*   Modified - You have made changes to the file but have yet to commit it to your database
*   Staged - You have marked a modified file to go into your next commit snapshot

This will naturally lead to three sections of a git project:

*   Git Directory  - also known as the repository or repo, this is where the files are stored when committed.  This is the most important area for your Git project as it is where all of the metadata and the object database for your project is stored.  It is what is copied when you clone a repo from another computer.
*   Working Directory - this is where the files are stored when you are working on them.  It is also what you get when you check out a version of the project.  The files are pulled out of the database and placed on disk for you to use.
*   Staging area - also known as the 'index', this is generally contained in your Git directory.  It stores information about what will go into the next commit.

### Git Interfaces

Raw Git is simply an application that runs in the Linux terminal.  However, it is open source and therefore there are many other ways to access it.  For instance, you have likely heard of GitHub, which is simply a web-based interface for Git.  There are other, more GUI based websites that make the learning curve a little easier, such as GitLab, Savannah, BitBucket, and SourceForge.   I need to start _really_ using Git.  I've put it off for a long time, but I feel like I shouldn't put it off any longer.  Lets start our first Git repo!

### Create Your First Repo

First, go to the [Git download page](https://git-scm.com/download) and download the correct version for your operating system.  I used all of the default settings in the installer setup.  After it installs, we can take the easy way out by searching for the Git GUI.  I'm here to learn Git, so I'll try my hand at creating a repo from the terminal. If the terminal did not start automatically, search for the 'Git Bash' application. Next, create a folder for your new project on your computer and navigate to it within Bash.  I am working on a Disney Pin Tracking software that I have already started, so I navigated to that folder.  Type:

git init

This will create a folder called **.git** that has several files and folders within it.  At this point, nothing is tracked yet.  To start, I'll commit my current progress on this project:

git add Pins/*.*
git commit -m 'initial project version'

My actual project files are located in a folder called Pins within the directory Pins.  So, to add all of these files I use the **git add Pins/*.*** line.  Finally, I commit with the **git commit** command. To see if there are any files that are not being tracked, we use the command:

git status

This resulted in:

$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        .vs/
        Pins.sln
        Pins/My Project/
        Pins/bin/
        Pins/obj/

Unfortunately, using *.* did not capture all of the files!  I used the git add command for each line listed in the output above and recommitted.  Afterwards, I checked the status again and got a better result:

$ git status
On branch master
nothing to commit, working tree clean

If we change a file in our branch and run the git status again, it will tell us that changes have been made.  I made a simple change to the GUI in my project, which resulted in the following status:

$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   Pins/Form2.Designer.vb

no changes added to commit (use "git add" and/or "git commit -a")

### Skip The Staging Area

The staging area is where we use the **git add** command.  This can be really useful, especially if you want to commit things separately.  However, it is another step that might not be wanted or needed.  To skip it, use the command:

git commit -a -m 'commit message'

While this is useful, be very careful that you don't accidentally add changes that you didn't want committed.   I think this is enough for me to get started using Git.  There are a lot of resources out there, and I'm glad to finally have my foot in the door with grasping a better understanding!  I've used GitHub a lot, and I've even used Git some.  However, until now I have just fumbled around with it.  I'm really excited to start building a foundation and truly understanding what I'm doing and using Git to my advantage. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._