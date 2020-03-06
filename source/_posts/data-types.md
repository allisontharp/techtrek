---
title: Data Types
tags:
  - MySQL
url: 78.html
id: 78
categories:
  - Methodology
date: 2015-11-17 08:00:09
---

We briefly covered data types in our post on building your first database [here](/building-your-first-database/), but I wanted to dive a little bit deeper into the subject. When you enter data into a MySQL database, it will fall into one of three categories:

*   Numeric
*   String
*   Date & Time

Numeric There are many options if you want all of the data in a particular column to be a number.  If you know what range the numbers will all fall in, you should pick the numeric type that fits all of the numbers in that range without going too much over.  That way, the system doesn't try to save too much space for your numeric column.

*   TINYINT: 127 to -128
*   SMALLINT: 32,768 to -32,767
*   MEDIUMINT: 8,388,608 TO -8,388,608
*   INT: 2^31 to -2^31-1
*   BIGINT: 2^63 to -2^63-1
*   FLOAT: Decimal spaces, 1.1E38 to -1.1E38
*   DOUBLE: decimal spaces, 1.7E308 to -1.7E308

Strings Strings are characters.  They can be letters, numbers, or special characters.  However, the system doesn't recognize the numbers as numbers, so you can't easily do math on them.  In general, if you have a string type you will need to specify how long you expect the string to be.  For instance, if you have a column for first name, you may choose VARCHAR(20).  This indicates that the column can have any number of characters in it but you don't expect the length of the first name to go over 20 characters.  As with numeric types, you shouldn't excessively overestimate this number.  Try to be as realistic as possible.

*   CHAR: a character string with a fixed length.  Ex: CHAR(5).
*   VARCHAR: a character string with a variable length.
*   BLOB: can contain 2^16 bits of data
*   ENUM: a character string that has a limited number of possible values, which you must define.
*   SET: a list of possible character strings.  Unlike ENUM, a SET can contain multiple values in comparison to the one legal value with ENUM

Date & Time Finally, we have date and time types.  You could certainly do this with string types or numeric types.  However, using date & time will ensure that all of the dates and times are in the same format.  You won't have to worry if month or date is first.

*   DATE: always in the form YYYY-MM-DD
*   TIME: HH:MM:SS
*   DATETIME: YYYY-MM-DD HH:MM:SS
*   TIMESTAMP: YYYYMMDDHHMMSS
*   YEAR: YYYY