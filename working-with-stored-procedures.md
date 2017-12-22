# Work in progress
# How to be productive while working with Stored Procedures in SQL Server

## What's wrong with databases?
I won't try to sell you the idea that stored procedures are the best and that everybody needs to throw away all their ORM code and move to the database world. I believe that every team has to choose the best tool that, first helps them to solve their problem and second makes the team productive.

Whether you landed on a project with many stored procedures, you're a DB geek or you just simply find that stored procedures is the right tool for you job, 
you will face many challenges if you want to keep your team productive while working on these kind of projects.

The problem sometimes is that, even when coding a feature in a stored procedure is the right choice, developer wouldn't go that way just because they won't be productive on that technology, and it would be more likely because of one of these problems:

### Centralized
Teams tend to have only one SQL Server with a bunch of different versions of the same database, e.g. Dev, Test or CI.

So, if one developer makes a change in the DEV database it will affect others developers **immediatelly**. Some teams will try to mitigate this problem trying to alter the database as little as possible, and when they do so they would @channel at Slack (no please don't @channel), send an email or even yelling at the office.

### Source control
Next is source control Very few teams put their database in source control, How would you do that? we'll get there. Devs would commit DDL scripts and even stored procedure changes.

### Debuging
Are you telling me that people debug procedures? Yes some, I think 2 developers do that, but it's possible

## Being productive

### Decentrilize

### Stored procedures are part of the code

### You need data to work

### DB Syncing tips

