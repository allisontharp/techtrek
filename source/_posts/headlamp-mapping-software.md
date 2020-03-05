---
title: Headlamp Mapping Software
tags:
  - matlab
  - Python
url: 1308.html
id: 1308
categories:
  # - Programming
date: 2016-05-20 08:00:03
---

I used to work as an Optical Engineer designing exterior automotive lighting systems (headlamps, tail lamps, auxiliary lamps, etc).  I was the lead engineer for Ford projects.  My biggest project was probably the 2015 Ford Edge: ![headlight](http://www.techtrek.io/wp-content/uploads/2016/05/headlight-300x200.jpg) The federal government has a lot of regulations on everything in your vehicle, lighting systems included.  Most people don't know it, but headlamp patterns are meticulously designed to meet strict requirements that cover all aspects of the lamp including light output, size, distance of different functions (high beam, low beam, turn, running lamp, etc) to each other, etc.  As an Optical Engineer, all of these regulations were very important to me.  However, when designing the optical elements of the lamp I was mostly concerned with the light output regulations.

### Background

If a person were so inclined, they could have an entire blog dedicated to the ever changing federal and state regulations of exterior automotive lighting systems.  As a general overview, the lighting requirements depend on the function.  In general, the light coming out of your headlamp is considered the _beam pattern_.  As you might expect, low beam patterns are different by design and regulation than high beam patterns.  When shined onto a wall, a low beam pattern will look something like this (for a single headlamp): ![Capture](http://www.techtrek.io/wp-content/uploads/2016/05/Capture-1.jpg) Where the vertical and horizontal red lines meet is called _HV_ for horizontal-vertical.  If we put a laser at this point and shined it back to the lamp, it would shine right back to the low beam bulb.  The standard plot like the one imaged above works in angles.  The numbers on the bottom represent the horizontal angles in degrees and the numbers on the left represent the vertical angles, also in degrees.  The federal government will require a certain amount of light for the ray that hits a particular point in the angular space.  For instance, the federal government requires that at 0.6 degrees down and 1.3 degrees right, the light ray must have at least 10,000 candela (a measure of light).  The standard way we would talk about this point is to say "0.6D, 1.3R". The regulation also requires a certain amount of light in entire areas or across a line, in addition to points.  In order to measure a headlamp, automotive companies use a large and expensive piece of equipment called a _goniometer_. ![opsira2_100813](http://www.techtrek.io/wp-content/uploads/2016/05/opsira2_100813.jpg) In general, you mount the lamp onto the robotic arm and it moves around.  A light sensor will be placed far away and the robotic arm will move such that the sensor can essentially scan the lamp.  This test takes about 45 minutes.  If you need fine measurements, the test will take even longer.

### The Problem

Goniometers are really great pieces of equipment for companies that have the time, money, space, and need.  However, some people may want to measure a light source but they can't afford this machine or maybe they don't need the measurements to be exactly accurate (just relatively precise).  I was faced with this problem after I left my automotive engineering job.  I needed to be able to relatively measure light sources (headlamps and flash lights, mostly) but couldn't afford a goniometer (in terms of money or space!). In order to get around this, I developed a Matlab program and photographical process to measure light sources.

### Process Overview

In general, the process I developed is:

1.  Turn the light source on, mount it to something stable (if possible), and shine it onto a white surface.  Mount a DSLR camera as close to the light source as possible.
2.  Turn off all other light sources and take a well exposed image of the light source shining onto the white surface.
3.  Take photos at +- 1 and 2 exposures
4.  Take a few light readings using a photometer

After this is done, I can import the images into the software I developed.  This software is currently being used by a company and therefore the details can't be published.  However, the process that the user follows is:

1.  Import the photos
2.  Tell the software how far away the light source was from the wall
3.  Import the light readings

From here, the software converts the photos into a single High Dynamic Range (HDR) image and calculates what the intensity of every ray in the image is.

### Software Features

This was my first big software development process and I am extraordinarily proud of it.  I learned a _lot_ with this project, particularly because I was the only person working on it.  Some of the more interesting features of the software are:

1.  User can input headlamp height above ground for a specific vehicle, headlamp separation, the location of an object in the roadway in front of the vehicle to get a simulation of how much light would be following on to the object.
2.  User can input the same information above in addition to speed and direction for vehicle and objects and run a simulation of how much light would reach the object over time or distance.
3.  User can input an angular space and receive the light on that ray (or the user can click on the HDR image instead of inputting the angular space).
4.  The user can input the above information and can get a _ground plot_, which shows how much light reaches the ground in front of the vehicle.
5.  "Average Minimum Headlamp" - the software takes the federal requirements and developed a simulation for the average minimum headlamp (low beam and high beam) to meet the federal requirements.  The user can do all of the above functions with this simulated headlamp, which is useful if the user doesn't have an actual measurement or just wants to test something quickly.
6.  The user can generate two separate reports:
    1.  Test against the federal photometric requirements and see if the lamp meets.
    2.  Input specific test points and get the amount of light at each test point.

### Conclusion

This project taught me a lot about Matlab and software design in general.  I had a lot of fun developing it and even several year later, find myself occassionally coming back to it to add additional features or make some portion of it more efficient.  I redid this software in Python, mostly to see if I could (but also because I thought it would be more portable).  That process was fascinating and a lot of fun.  I'm really thankful to have had the opportunity to use my optics knowledge while also building my programming knowledge.  The software is not as accurate as a goniometer but it is not intended for use in an environment that needs measurements which are very, very accurate.  There are pros and cons to both systems. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._