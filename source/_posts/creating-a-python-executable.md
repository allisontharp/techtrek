---
title: Creating a Python Executable
url: 782.html
id: 782
categories:
  - How To
date: 2016-03-08 08:00:14
tags:
  - Python
---

I made a Python script for work that puts all of the [JPEG images in a folder into a PDF file](/creating-a-summary-pdf-of-images/).  In order to make it useful for my coworkers (who don't have or use Python), I wanted to make it a stand alone executable.  Creating a Python executable is a lot easier than it might seem.  I will assume you already have and understand Python (if not, start [here](https://www.python.org/about/gettingstarted/)).  You'll also need to download [py2exe](http://www.py2exe.org/).  I will also assume you already have a python script that you to turn into an executable.  For this tutorial, I will call this file _myfile.py_.

##### 1 Create a setup file

I usually call this file _setup.py_, but you can call it anything.  This script should only contain the lines: \[python\] from distutils.core import setup import py2exe setup(console=\['test.py'\]) \[/python\]

##### 2 Run the setup file to create the executable

Next, go to the command prompt (windows key + "cmd") and type: \[text\] python setup.py py2exe \[/text\] This runs the setup.py file and creates the executable.  You should see an output window that says something like "Your executable(s) also depend on these dlls which are not included, you may or may not need to distribute them."  Following that, there will be a list of .dll files that were not included: ![2016-02-25_08h10_22](/wp-content/uploads/2016/02/2016-02-25_08h10_22.png)

##### 3 Test the executable

The setup.py script will create a directory called 'dist', which includes the .exe file.  You can run the executable by navigating to the folder and double clicking the .exe.  Alternatively, you can type into the command prompt to test the executable: \[text\] cd dist myfile.exe \[/text\] The chances are pretty high that the executable will work on your computer if the original Python script was working on your computer.  However, if this software will be used on another machine, it is a good idea to test it out before you distribute the software.  Other machines may not have all of the dependencies that yours has.  If this is the case, you will receive an error message listing which .dll file needs to be included in the 'dist' directory.  Adding that .dll file should allow the software to run. If you have any questions or suggestions, feel free to let me know in the comments or [contact me](/contact/)!