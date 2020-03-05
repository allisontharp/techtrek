---
title: Rubiks Cube Program
tags:
  - vb
url: 1378.html
id: 1378
categories:
  - Personal Projects
---

I've always been interested in Rubiks Cubes.  I have a pretty large collection and there were many years when I was younger that I was obsessed with solving it as fast as I could.  My best time ever was 11.00 seconds.   Three years ago, I was interested in learning more about programming and decided to make a program that would allow a user to input a scrambled cube and then it would output a solution.  I had high hopes that I would eventually grow the program into allowing the user to just take pictures of the cube and it would automatically figure out how it was scrambled.  Unfortunately, life got in the way and I never finished the program.  It popped up on Facebook's _On This Day_ and I'm interested in redoing the program.

### ![collection](http://www.techtrek.io/wp-content/uploads/2016/06/collection-225x300.jpg)Choosing The Language

When I did the program the first time around, the only language I knew was Matlab.  I tried to do it in Python at the same time to learn Python, but I quickly learned that Python's GUI capabilities were a hassle and I dropped the Python portion of it.  This time around, Python is my strongest language.  However, I don't think I would learn very much during this project if I did it in Python.  Instead, I'm choosing to use Visual Basic.  I know VB pretty well but I haven't had nearly as much experience with it as I have had with Python.  Also, since this is a GUI based program, VB makes a lot of sense!

### Choosing The Solving Method![cubesolver](http://www.techtrek.io/wp-content/uploads/2016/06/cubesolver-225x300.jpg)

There are many methods for solving a Rubiks Cube depending on what you are wanting to get out of the solve.  During Rubiks Cube competitions, there are events for speedsolving (solving it as fast as possible), number of moves (solving it in as few moves as possible), blindfolded (solving it completely and quickly blindfolded), and feet (solving it with your feet).  Each of these typically have methods that are good for that particular event but not as good for another event. The easiest way to programmatically solve a Rubiks Cube, in my opinion, is to use the blindfold method.  This is because most blindfold solving methods are designed such that when you place pieces in their correct place, the other pieces do not move around.  This will allow us to not have to track the pieces!  The downfall to this method is that the solution will be very, very long.  Below is an output from my program from before.  Believe it or not, this only solves the edges! ![Capture](http://www.techtrek.io/wp-content/uploads/2016/06/Capture-1-300x267.jpg) Sorry for the terrible image quality.  I don't have this program any more, so the image is from a terrible quality video that I took 3 years ago.  However, as you can see, the solution is huge!  This is a downfall of using the blindfold method; however, the point of the program isn't to solve the cube efficiently, just to solve it.

### Rubiks Cube Terminology

I'm not intending to teach anyone how to solve a cube -- there are entire website and YouTube channels dedicated to solving the Rubiks Cube.  If you are interested in learning how to solve one, [this video](https://www.youtube.com/watch?v=HsQIoPyfQzM) is a popular place to start.  However, so that we are on the same page, there are some things that I want to let you know about a Rubiks Cube (these are generally true for all cubes, but I'm specifically talking about the typical 3x3 cube):

*   A Rubiks Cube has a _core_ which has 6 _center pieces_attached to it.  The center piece is the piece in the middle of each side.  These pieces _never move_.
*   There are, in general, two color schemes for a Rubiks Cube: Western and Japanese.  In America, the Western color scheme is much more common.  However, my first cube used Japanese and therefore I use Japanese.  The Japanese color scheme is:

![rubiks-cube-japanese-color-scheme](http://www.techtrek.io/wp-content/uploads/2016/06/rubiks-cube-japanese-color-scheme.jpg)

*   With the Japanese color scheme, the white side (and therefore the white center piece) is always across from the blue side (and therefore the blue center piece).  Orange is always opposite red; green is opposite yellow.
*   An _algorithm_ is simply a combination of upper and lower case letters and special characters that describes how to rotate the sides of the cube.  An algorithm typically has a goal (such as combine an _edge_ and a _corner_ piece and move it to the correct position).
*   In addition to the core and center pieces, the cube has 12 _edge pieces_ and 8 _corner pieces_.  The edge pieces have two sides (such as the white/green edge piece).  The corner pieces have three sides (such as the white/yellow/orange corner).  Most non-cubers think about a cube as a collection of 54 stickers -- it isn't!  It is a collection of 6 non-moving center pieces, 12 edge pieces, and 8 corner pieces.
*   The moving pieces also have an orientation.  This means that the white/green piece might be in the correct _position_ but the green sticker is next to the non-moving white center piece and the white sticker is next to the non-moving green corner piece.

#### Algorithm Basics