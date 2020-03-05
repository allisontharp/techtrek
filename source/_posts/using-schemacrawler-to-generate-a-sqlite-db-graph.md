---
title: Using SchemaCrawler to Generate a SQLite DB Graph
tags:
  - Administration
  - SQLite
  - Visualization
url: 722.html
id: 722
categories:
  - How To
date: 2016-03-11 08:00:54
---

### Overview

According to its [website](http://sualeh.github.io/SchemaCrawler/), SchemaCrawler is a "free database schema discovery and comprehension tool".  One of the many things it can do is export a database diagram, which is useful if you are writing a blog about your database but more importantly if you are new to a database.  Below is an example output for this tool from a previous post: ![sc.64pc6023](http://www.techtrek.io/wp-content/uploads/2016/02/sc.64pc6023.png) Before SchemaCrawler, I viewed schemas as a planning tool -- something to do at the beginning of the project that is used as a guide for building a database.  However, SchemaCrawler generates a schema based on your _already existing database_.  This really opened my mind up to the power of a schema.  Since I now know how to generate a schema based on any database, I could use this tool to help debug issues with my database or even to help me understand someone else's database.  It is almost like getting a roadmap for a database and I think this tool has the potential to be very powerful for me in the future.

### Setting Up SchemaCrawler

Pre-requisites: - Java needs to be installed and added to your Path variable (I am using [Java Runtine Environment 1.8.0_73](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)) \- Relational database I've only used SchemaCrawler with SQLite, but I believe it is compatible with any of the major RDMS clients you are using.  Download SchemaCrawler from their official [GitHub page](https://github.com/sualeh/SchemaCrawler/releases/). Since we are wanting to create a database diagram, we also need to install [GraphViz](http://www.graphviz.org/).  This is not necessary for SchemaCrawler to run but is necessary for it to generate the image output.  After GraphViz is installed, add it to your Path variable as well.

### Generating A Graph of Your Database

After everything is set up, navigate to the schemacrawler/\_schemacrawler directory from the command prompt.  Then, type: \[text\] schemacrawler -server sqlite -database C:\\YOUR\\DATABASE\\LOCATION\ -infolevel=maximum -password= -command graph \[/text\] where C:\\YOUR\\DATABASE\\LOCATION\ is the path to your SQLite database.  After the script runs, it will generate a .PNG image within the \_schemacrawler directory.  This image will be a graph of the database you chose! I haven't used this tool beyond just generating these simple graphs, but I am really looking forward to diving a bit deeper into it.  I think it will be a great tool in my toolbox for understanding and troubleshooting databases in the future. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._