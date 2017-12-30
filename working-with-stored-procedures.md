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

Lets use our well-known Northwind database as an example. Lets create a new "SQL Server Database project"

![New Project](https://raw.githubusercontent.com/kblok/tech-posts/master/images/newproject.png)

#### Import your current database

I bet that you already have a database, if so, lets import all the database definition using the import tool:

![Import option](https://raw.githubusercontent.com/kblok/tech-posts/master/images/importoption.png)
  
Then you will need to setup the database connnection and import settings, lets keep this simple:

![Import wizard](https://raw.githubusercontent.com/kblok/tech-posts/master/images/importwizard.png)
 
 Voilà! You know have yur database ready to be committed into your repository.
 
 ![DB project](https://raw.githubusercontent.com/kblok/tech-posts/master/images/dbproject.png)
 
### Decentralized

Perfect, now we have our database under source control, now What?
The next thing you must have is one database per developer which he can have the freedom to update and recreate when needed. You can accomplish this in 3 ways, ok there might be more that 3 ways but these are the 3 ways I know:

#### One database in a shared server
This could seems as the easiest solution if you already have one server used by all developers. You could use a naming convention to id each database, super super smart as `<MyProject>Dev<DevName>`.

##### Pros
* There is no much more to setup, just create one database per dev and thats all.
* Collaboration. Devs can access each other database making easier to help each other.

##### Cons
* If you are not using a shared server right now you might need to setup one server and think about security, VPNs and things like that.
* It might be a slower experience for the developer compared with a local environment.
* One developer might slow down the only server if his doing some heavy task.

#### LocalDB

>LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development.

You might not know but if you have Visual Studio installed there is a high chance that you already have a LocalDB installed. You can check this out by opening the **SQL Server Object Explorer**

<SQL Server object explorer Image>
  
If you don't find your LocalDB you can see how to install it (here)[https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/sql-server-2016-express-localdb]. There are another versions you can install, for instance the full (Microsoft SQL Server Developer Edition)[https://blogs.technet.microsoft.com/dataplatforminsider/2016/03/31/microsoft-sql-server-developer-edition-is-now-free/]

##### Pros
* Your localDB is just there you just need to use it
* It's a localDB you won't need a VPN or request access to a server

##### Cons
* As it will run on your hardware, running a SQL Server process could be quite expensive for you computer, slowing down your development process.

#### Local Server on Docker

This solution is similar to the localDB one but if you are using MacOS and run Visual Studio on a Virtual Machine running your SQL Server on a docker container on the MacOS this will speed up your Virtual Machine, I recommend this solution if you use MacOS.

You can read about SQL Server running on docker [here](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker)

### You need data to work



### DB Syncing tips

### CI
