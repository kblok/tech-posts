# Work in progress
# How to be productive while working with Stored Procedures in SQL Server

## What's wrong with databases?
I won't try to sell you the idea that stored procedures are the best and that everybody needs to throw away all their ORM code and move to the database world. I believe that every team has to choose the best tool that, first helps them to solve their problem and second makes the team productive.

Whether you landed on a project with many stored procedures, you're a DB geek or you just simply find that stored procedures is the right tool for you job, 
you will face many challenges if you want to keep your team productive while working on these kind of projects.

The problem sometimes is that, even when coding a feature in a stored procedure is the right choice, a developer wouldn't go that way just because they won't be productive on that technology, and it would be more likely because of one of these problems:

### Centralized
Teams tend to have only one SQL Server with a bunch of different versions of the same database, e.g. Dev, Test or CI.

So, if one developer makes a change in the DEV database it will affect others developers **immediatelly**. Some teams will try to mitigate this problem trying to alter the database as little as possible, and when they do so they would @channel at Slack (no please don't @channel), send an email or even yelling at the office.

### Source control
Next is source control Very few teams put their database in source control, How would you do that? we'll get there. Devs would commit DDL scripts and even stored procedure changes.

### Debuging
Are you telling me that people debug procedures? Yes some, I think 2 developers do that, but it's possible.

## Being productive
These are some tips and tool I've found with my team over time and helped me to be productive while working with databases.

### The database is parte of your code
The database is part of your code, even if you don't use stored procedure, your tables definitions are part of your code. Ok you might say "I'm a code first developer" and that's fine, this post won't help you at all.
But if you start a "database first" developer, the table definition should be part of your code, if you add a new field in a table you will need to create a branch add that new field in the table definition and commit it in the same branch as the code using that new field.

The benefit is quite obvious, when your PR is merged not only your code will be merged but also that new field in the database.

#### Introducing DB Projects in Visual Studio
So how can this be accomplished?

I found in Visual Studio the solution for this problem. Lets take a look at this:

##### Creating a DB Project

Lets use our wellknown Northwind database as an example. Lets create a new "SQL Server Database project"

![New Project](https://raw.githubusercontent.com/kblok/tech-posts/master/images/newproject.png)

#### Import your current database

I bet that you already have a database, if so, lets import all the database definition using the import tool:

![Import option](https://raw.githubusercontent.com/kblok/tech-posts/master/images/importoption.png)
  
Then you will need to setup the database connnection and import settings, lets keep this simple:

![Import wizard](https://raw.githubusercontent.com/kblok/tech-posts/master/images/importwizard.png)
 
 Voilà! You know have yur database ready to be committed into your repository.
 
 ![DB project](https://raw.githubusercontent.com/kblok/tech-posts/master/images/dbproject.png)
 
### Decentralized


### You need data to work

### DB Syncing tips

