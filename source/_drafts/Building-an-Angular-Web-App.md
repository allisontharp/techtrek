---
title: Building an Angular Web App
tags:
  - Angular
  - boardgames
url: 2045.html
id: 2045
categories:
  - Personal Projects
---

I recently have gotten into playing Pokemon games. I wanted an app or website that I could use to track which Pokemon I've caught in each game I'm playing as well as search for where to find the Pokemon I'm missing.

Set up Angular CLI
------------------

The first step is to install the Angular CLI. This is the official Angular tool for initializing and working with Angular projects. Run the following code in the command prompt:

    npm install -g @angular/cli@next 

Create a new Angular project
----------------------------

To create a new Angular project, complete with the basic folders and files that you'll need, run the following command:

    ng new <project name>

You will be asked a few questions. I said Yes to add Angular routing and chose CSS as the style sheet.

Test the project
----------------

Test the project by first CDing into your project's directory and then running:

    ng serve

This should allow you to use your browser and go to http://localhost:4200. It will look like this:

![](http://www.techtrek.io/wp-content/uploads/2020/02/Screen-Shot-2020-02-02-at-6.48.39-PM-1024x550.png)

Generate a Component
--------------------

In Angular, each aspect of the site is a component. You can group multiple things together into one large component or have a small section of the site be its own component. The first