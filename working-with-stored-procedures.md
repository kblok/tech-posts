# Work in progress
# How to work with stored procedures and not die trying 

## What's wrong with databases?
I won't try to sell you the idea that stored procedures are the best and that everybody needs to throw away all their ORM code and move to the database world. I believe that every team has to choose the best tool that, first helps them to solve their problem and second makes the team productive.

Whether you landed on a project with many stored procedures, you're a DB geek or you just simply find that stored procedures is the right tool for you job, 
you will face many challenges if you want to improve your team's productity.

The problem I see sometimes is that, even when coding a feature in a stored procedure is the right choice, a developer wouldn't go that way just because they won't be productive on that technology, and it would be more likely because of one of these problems:

### Centralized
Teams tend to have only one SQL Server with a bunch of different versions of the same database, e.g. Dev, Test or CI.

So, if one developer makes a change in the DEV database it will affect others developers **immediatelly**. Some teams will try to mitigate this problem trying to alter the database as little as possible, and when they do so they would @channel at Slack (no please don't @channel), send an email or even yelling at the office.

### Source control
Next is source control. Very few teams put their database in source control, How would you do that? we'll get there. Devs would commit DDL scripts and even stored procedure changes but they wouldn't commit the database itself.

### Debuging
Are you telling me that people debug procedures? Yes some, I think 2 developers do that, but it's possible.

## Being productive
These are some tips and tools I've found with my team over time that helped us to be productive in this scenario.

### The database is part of your code
The database is part of your code, even if you don't use stored procedures, your tables definition are part of your code. Ok, you might say "I'm a code first developer" and that's fine, this post won't help you at all.
But if you start a "database first" developer, the table definition should be part of your code, if you add a new field in a table you will need to create a branch add that new field in the table definition and commit it in the same branch as the code using that new field.
The benefit is quite obvious, when your PR is merged, not only your code will be merged but also that new field in the database.

#### How can this be accomplished?
I found in Visual Studio the solution for this problem. Let's take a look at this:

##### Creating a DB Project

Let's use our well-known Northwind database as an example. Just go to Visual Studio, click "New project" and look for "SQL Server Database project":

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/newproject.png" width="800px">

#### Import your current database

I bet that you already have a database, if so, you will be able to import all the database definition using the import tool:

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/importoption.png" width="600px">

Then you will need to setup the database connnection and import settings, let's keep this simple:

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/importwizard.png" width="600px">

Voilà! You now have yur database ready to be committed into your repository.
 
<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/dbproject.png" width="400px">
 
### Decentralized

Perfect, now we have our database under source control, now what?

The next thing you must have is one database per developer where he can have the freedom to update and recreate when needed. You can accomplish this in 3 ways, ok there might be more that 3 ways but these are the 3 ways I recommend:

#### One database in a shared server
This could be the easiest solution if you already have one server used by all developers. You could use a naming convention to identify each database, something super smart such as `<MyProject>Dev<DevName>`.

##### Pros
* There is no much more to setup, just create one database per dev and thats all.
* Collaboration. Devs can access each other database making easier to help each other.

##### Cons
* If you are not using a shared server right now you might need to setup one server and think about security, VPNs and things like that.
* It might be a slower experience for the developer compared with a local environment.
* One developer might slow down the only server you have if his performing some heavy task.

#### LocalDB

>LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development.

You might not know but if you have Visual Studio installed there is a high chance that you already have a LocalDB installed. You can check this out by opening the **SQL Server Object Explorer**

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/SQLServerObjectExplorer.png" height="200px">
  
If you don't find your LocalDB you can see how to install it [here](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/sql-server-2016-express-localdb). There are another versions you can install, for instance the full [Microsoft SQL Server Developer Edition](https://blogs.technet.microsoft.com/dataplatforminsider/2016/03/31/microsoft-sql-server-developer-edition-is-now-free/)

##### Pros
* Your localDB is just there you just need to use it
* It's a localDB you won't need a VPN or request access to a server

##### Cons
* As it will run on your hardware, running a SQL Server process could be quite expensive for you computer, slowing down your development process.

#### Local Server on Docker

This solution is similar to the localDB one, but if you are using MacOS and run Visual Studio on a Virtual Machine, running your SQL Server on a Docker container on the MacOS this will speed up your Virtual Machine. I recommend this solution if you use MacOS.

You can read about SQL Server running on Docker [here](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker)

### You need data to work with

As each developer would have his own database, having data to create a useful and ready to use database becomes another very important piece of the project. It's very easy to create all these data if you already have an existing database.

First let's create a Data folder where we'll save all our insert script data.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/CreateDataFolder.png" height="200px">
  
[SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) has very cool tool to generate scripts, it is call... drum rolls... Generate Scripts!
 
<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/GenerateScripts.png" height="400px">
  
Once there we need to pick the tables we want to generate:
 
<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/PickTables.png" width="600px">
  
Then we select the location, we would need to select the data folder we created on the SQL project

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/SelectLocation.png" width="600px">

We need to go to the Advanced option and select that we only want the data:

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/SetupDataOnly.png" width="400px">
  
And we are done!

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/Voila.png" width="600px">
  
Now if we go back to the SQL Project we'll see all the scripts we just generated, let's include those files in the project.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/AddScriptsToProject.png" height="200px">

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/AddScriptsToProject2.png" height="200px">
 
By default, when we include files in a project, the Build Action is set to **Build**, but as these files are just insert scripts we need to set them to **None**.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/DoNotBuildDataScripts.png" height="200px">
  
So, How do we make use of these scripts? We need to create a Post Deployment Script and include all these inserts there.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/PostScriptFile.png" width="800px">
  
So now we can complete this file with all the insert scripts we created before.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/CompletePostBuildScript.png" width="800px">
  
### DB Syncing

Perfect! Now we have a SQL Project, with our tables, our stored procedures and even our test data! Now we have to use it.

#### Publish tool

Publish is the tool you would use when you want to create the database from scratch. So let's go to the publish option:


<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/PublishOption.png" width="400px">
  

Once there we just need to pick our connection string and the destination database.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/PublishSelectDatabase.png" width="400px">
 
Under the advanced section there are some interesting options, like "Always re-create database" or "Block incremental deployment if data loss might occur".

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/PublishAdvanced.png" width="400px">

Then just hit publish and voilà you have a new database with all your tables populated and ready to use!

#### Schema Compare tool

The compare tool will be the option that you would use every day, as you might guess, it will compare your SQL code and your database and impact those changes.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/SchemaCompareOption.png" width="400px">

Once there you will be able to see not only what objects are going to be changed but also what are the changes.

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/SchemaCompareDifferences.png" width="800px">

<img src="https://raw.githubusercontent.com/kblok/tech-posts/master/images/working-with-stored-procedures/SchemaCompareDifferencesDetails.png" width="800px">

Before hitting "Update" you might want to go the Schema compare options and uncheck "Block on possible data loss" because if you want to impact the changes made in another branch you might want to accept that data will be lost.

#### ProcUpdater

Schema compare it's perfect at his job, it can create a databse from scratch, it can create users, schemas, it can handle table and column renames, everything. But it can slow down your development process. The compare step might take 3 minutes and the Update itself another 3 minutes, if you do that ten times a day you would have waste 1 hour waiting for the DB to sync up.

So I created an small app called [ProcUpdater](https://github.com/kblok/ProcUpdater) (yes, I was inspired that day). ProcUpdate will watch your Stored Procedures folder and impact your stored procedures changes immediately. It can even impact all the stored procedures changes when you switch branches. It's not perfect, it won't impact table changes, but it could save you 1 hour a day! 

### CI

This workflow fits perfectly if you need to run integration tests in a CI environment. If you use a Windows based CI environment like [AppVeyor](https://www.appveyor.com/) you will be able to deploy your database in that environment every time you build a branch. You just need to run `msdeploy.exe`. It could be something like this:

```
msbuild "NorthwindDB.sqlproj" /p:Configuration=Release /p:Platform="Any CPU"
"C:\Program Files\IIS\Microsoft Web Deploy V3\msdeploy.exe" -verb:sync -source:dbDacFx="NorthwindDB.dacpac" -dest:dbDacFx="...",dacpacAction=Deploy,CreateNewDatabase=True'
```

### Final words

I use to say that most of the time you can hide complexity but not remove it completely. If your project is using lots of stored procedures we cannot remove that complexity, and we didn't talk about how to deal with staging environments, but we can speed up the process and make your team much more productive.


