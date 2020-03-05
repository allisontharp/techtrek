---
title: Badge Generator in Python
tags:
  - Python
url: 1807.html
id: 1807
categories:
  - Uncategorized
date: 2016-10-18 08:00:48
---

I volunteered to help out a local board game convention this year.  The organizer is a friend of mine and has helped with my own board game event (a cancer research fundraiser called [Play It Forward](http://www.playitforwardfundraiser.com/)).  To help out, Play It Forward is providing badges, badge holders, and lanyards.

The Goal
--------

The organizer of the convention sent me a Word file with the first and last names of all of the participants.  The goal was to write a Python script that could take a file with the names and output a PDF file with six 3" x 4" badges per page.

The Badge
---------

I looked up the number of pixels per inch (known as DPI or dots per inch) that a high quality printer prints at.  It turns out, 300 DPI is good for pretty high quality printing.  I know that the badges should be 3" x 4", so the number of pixels should for the badge should be (3x300, 4x300).  The badge design includes everything except the name (we will be filling those in with Python).  The design that I came up with was: ![border](http://www.techtrek.io/wp-content/uploads/2016/10/border-300x225.jpg)

The Script
----------

The flow of the script is pretty easy.  For every name, I open the badge template and put the first name near the top and the last name a little below that.  Some of the participants have very large last names, so I check the length (in pixels) of the last name and use a smaller font size when necessary.  Depending on the number of badges on the current page, I paste the badge with the name in the next available location. Originally, once 6 badges were pasted on the page, I saved the page as a temporary, one page PDF.  I then merged the one page PDF to the output PDF, created a new blank page, and started over with the next 6 names.  However, I quickly realized that creating a PDF added a lot of messy artifacts around the text on the badges.  I tried printing them anyways, but they looked pretty bad. I couldn't find a way to generate a PDF without losing quality, so I changed the methodology slightly.  Instead of putting all of the badges into a multi-page PDF, I created multiple PNG images.  This is a little less convenient (I needed to print multiple PNG files instead of one PDF), but the quality is a **lot** better.  I also removed the date at the top of the badge template and instead added it in the script.    Both versions of the script are available on my [Gist](https://gist.github.com/allisontharp/c48714fe0e7c6f158d4d3ca517ac4c84). The entire script is below:

import glob
from PIL import Image, ImageDraw, ImageFont, PdfImagePlugin, _imaging ## was from PIL
from math import ceil
import os




badge_loc = 'border2.png'               # badge template
filename = 'names3.txt'                 # text file with names
dpi = 300


count = 0
size = (int(8.5\*dpi), int(11\*dpi))                # Size of paper to print (8.5x11)


numNames = sum(1 for line in open(filename))
numPages = int(ceil(float(numNames)/6))


pg = 0
for line in open(filename):

        firstname, lastname = line.split(' - ')
        
        badge = Image.open(badge_loc)
        width, height = badge.size
        
        draw = ImageDraw.Draw(badge)
        font = ImageFont.truetype('arial.ttf',75)

        
        msg = 'October 21st - 23rd 2016'
        w,h = draw.textsize(msg, font = font)
        draw.text(((width-w)/2, 150), msg, (179, 36,36), font=font)
        
        font = ImageFont.truetype('arial.ttf',200)
        
        msg = firstname
        w, h = draw.textsize(msg, font=font)
        draw.text(((width - w)/2, (height-h)/2-50), msg, (179, 36, 36), font=font)

        msg = lastname
        w, h = draw.textsize(msg, font=font)
        
        # Make font smaller for long last names
        if w >= 1400:
                font = ImageFont.truetype('arial.ttf',75)
                w, h = draw.textsize(msg, font = font)
        elif w >= 1100:
                font = ImageFont.truetype('arial.ttf',100)
                w, h = draw.textsize(msg, font = font)
        elif w >= 900:
                font = ImageFont.truetype('arial.ttf',150)
                w, h = draw.textsize(msg, font = font)
        elif w >= 750:
                font = ImageFont.truetype('arial.ttf',175)
                w, h = draw.textsize(msg, font = font)
        
        
        
        draw.text(((width - w)/2, (height-h)/2+200), msg, (179, 36, 36), font=font)
                
               
        if count == 0 :
                blank_image = Image.new("RGB", size, "White")
                blank_image.paste(badge, (0,0))
                blank_image.save('test1.pdf','pdf')
        elif count == 1:
                blank_image.paste(badge, (width+125, 0))
        elif count == 2:
                blank_image.paste(badge, (0, height+200))
        elif count == 3:
                blank_image.paste(badge, (width+125, height+200))
        elif count == 4:
                blank_image.paste(badge, (0, 3300-height))
        elif count == 5:
                blank_image.paste(badge, (width+125, (3300-height)))
                fname = str(pg)+".png"
                blank_image.save(fname,'png')

                

        if pg == numPages - 1 and count == (numNames%6)-1:
                print 'in here!'
                fname = str(pg)+".png"
                blank_image.save(fname,'png')
        
        if count == 5:
                pg += 1
                count = -1

        count += 1

This was a fun exercise.  I enjoy automating tasks with Python, so this was a good project for me.  I would have liked this to be a little bit more automated, particularly the section where I resize the last name font size.  However, the goal of this script was to more or less crank it out and just get the badges generated. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._