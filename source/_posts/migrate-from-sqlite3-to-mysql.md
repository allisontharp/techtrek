---
title: Migrate From SQLite3 To MySQL
tags:
  - MySQL
  - SQLite
url: 1320.html
id: 1320
categories:
#   - How To
date: 2016-05-27 08:00:03
---

I created a large SQLite database to store information on every game on www.boardgamegeek.com.  SQLite worked fine for this.  However, I was querying this database often and decided I needed a way to store these queries so that I could run them more easily.  Unfortunately, SQLite does not support this.  In addition, this is my first large database.  I think more people use MySQL for large databases than SQLite, so it will be good to have experience working with a large database in MySQL.  Finally, it will be good practice to migrate from one RDMBS to another. A **dump** is a file that has all of the SQL commands to completely restore your database.  It is a way to backup your database in such a way that you could literally rebuild it.  For us, we will be taking this dump and translating it into the SQL commands that MySQL recognizes and then rebuilding the database with those commands.

### Crate and Translate A Dump To MySQL Syntax

If your database is small, this would be a pretty easy thing to do.  However, if your database is large, like mine was, you will want a script to automate this for you.  There are several out there, but the one I used can be found [here](http://stackoverflow.com/a/87531/3115691).  To make it easier for you, the code is also below:

#!/usr/bin/env python

import re
import fileinput

def this\_line\_is_useless(line):
    useless_es = \[
        'BEGIN TRANSACTION',
        'COMMIT',
        'sqlite_sequence',
        'CREATE UNIQUE INDEX',
        'PRAGMA foreign_keys=OFF',
    \]
    for useless in useless_es:
        if re.search(useless, line):
            return True

def has\_primary\_key(line):
    return bool(re.search(r'PRIMARY KEY', line))

searching\_for\_end = False
for line in fileinput.input():
    if this\_line\_is_useless(line):
        continue

    # this line was necessary because ''); was getting
    # converted (inappropriately) to \\');
    if re.match(r".*, ''\\);", line):
        line = re.sub(r"''\\);", r'``);', line)

    if re.match(r'^CREATE TABLE.*', line):
        searching\_for\_end = True

    m = re.search('CREATE TABLE "?(\[a-zA-Z_\]*)"?(.*)', line)
    if m:
        name, sub = m.groups()
        line = "DROP TABLE IF EXISTS %(name)s;\\nCREATE TABLE IF NOT EXISTS `%(name)s`%(sub)s\\n"
        line = line % dict(name=name, sub=sub)
    else:
        m = re.search('INSERT INTO "(\[a-zA-Z_\]*)"(.*)', line)
        if m:
            line = 'INSERT INTO %s%s\\n' % m.groups()
            line = line.replace('"', r'\\"')
            line = line.replace('"', "'")
    line = re.sub(r"(\[^'\])'t'(.)", "\\1THIS\_IS\_TRUE\\2", line)
    line = line.replace('THIS\_IS\_TRUE', '1')
    line = re.sub(r"(\[^'\])'f'(.)", "\\1THIS\_IS\_FALSE\\2", line)
    line = line.replace('THIS\_IS\_FALSE', '0')

    # Add auto\_increment if it's not there since sqlite auto\_increments ALL
    # primary keys
    if searching\_for\_end:
        if re.search(r"integer(?:\\s+\\w+)*\\s\*PRIMARY KEY(?:\\s+\\w+)\*\\s*,", line):
            line = line.replace("PRIMARY KEY", "PRIMARY KEY AUTO_INCREMENT")
        # replace " and ' with ` because mysql doesn't like quotes in CREATE commands
        if line.find('DEFAULT') == -1:
            line = line.replace(r'"', r'`').replace(r"'", r'`')
        else:
            parts = line.split('DEFAULT')
            parts\[0\] = parts\[0\].replace(r'"', r'`').replace(r"'", r'`')
            line = 'DEFAULT'.join(parts)

    # And now we convert it back (see above)
    if re.match(r".*, ``\\);", line):
        line = re.sub(r'``\\);', r"'');", line)

    if searching\_for\_end and re.match(r'.*\\);', line):
        searching\_for\_end = False

    if re.match(r"CREATE INDEX", line):
        line = re.sub('"', '`', line)

    print line,

Save this script as _dump\_for\_mysql.py_ into the folder location where your SQLite database is.  Open the command prompt here and type:

sqlite3 <yoursqlitedatabase> .dump | python dump\_for\_mysql.py > dump.sql

This will create a file that has all of the CREATE and INSERT commands to create your database and insert all of your data.

### Import Into MySQL

First, open MySQL and create a database with whatever name you want it to be called.  Then, we can import our data into this database. To import this into MySQL, in the command prompt run:

mysql -u <yourusername> -p <yourpassword> <yourdatabasename> < <yourdumpfile.sql>

For example, mine looked like this:

mysql -u root -p boardgamecollection < dump.sql

For me this errored out a few times for a few different reasons.  The reasons were:

*   With SQLite, the CHAR limit is not mandatory.  Therefore, for many tables I had a CHAR limit of 50 but there were items with more than 50 characters.  This is okay with SQLite but illegal with MySQL.  For those tables, I had to open the _dump.sql_ file in a text editor and change the SQL command in the CREATE TABLE statement.
*   The first INSERT of _every_ table had the character ` instead of '.  To fix this, I opened the _dump.sql_ file in a text editor and changed the ` to '.
*   The tables that are set to AUTO\_INCREMENT already had values in those columns.  To fix this, I again opened the dump file and removed the AUTO\_INCREMENT requirement.  After the database was successfully built, I added this requirement to shoe tables by using the command:

ALTER TABLE publishers MODIFY COLUMN pubid INT auto_increment;

That's it.  The entire process didn't take too long.  It would have gone faster had the Python script worked properly.  If I have time, I may try to edit it so that at least the issue with the first INSERT command doesn't happen any more.  Another thing to keep in mind - if you have a large SQLite database, it will be even larger in MySQL.  My board game database was 17 MB in SQLite and 57 MB in MySQL.  I haven't played around with it too much to know which is faster but I intend on doing some performance testing with both of these. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._