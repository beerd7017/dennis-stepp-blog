+++
title = "Flyway, Gradle, Oracle JDBC"
description = "How to setup the Flyway Gradle plugin and connecting to an Oracle Database."
date = "2017-10-11"
categories = ['Automation', 'Programming', 'Database', 'Productivity']
tags = ['Gradle', 'Oracle', 'Flyway', 'Maven', 'Java', 'IntelliJ IDEA']
thumbnail = "img/posts/flywayGradle/post_main.png"
+++

I'm writing this blog post to hopefully help out some poor soul who is trying to automate their Oracle database deployments using the [Flyway](https://flywaydb.org) plugin for [Gradle](https://gradle.org/). This was kind of a pain to accomplish because at the time of this post the documentation was scattered about and difficult to piece together. Let's take care of that. ``</rant>``

# Assumptions
* You've created new projects in IntelliJ Idea before.
* You're somewhat familiar with how Gradle builds work.
* You're somewhat familiar with Oracle Database.
* You're new to Flyway

# Tools and Tech
* [JetBrains IntelliJ IDEA `2017.2.5`](https://www.jetbrains.com/idea/download/#section=windows)
* [Java JRE/JDK `1.7.0_25`](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Gradle `4.1`](https://gradle.org/)
* [Maven `3.5.0`](https://maven.apache.org/download.cgi)
* [Flyway `4.2.0`](https://flywaydb.org)
* [Oracle Database `11.2.0.4.0`](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* [Oracle JDBC Driver](http://www.oracle.com/technetwork/database/enterprise-edition/jdbc-112010-090769.html)

# Create a New Project

Create a new gradle project in IntelliJ IDEA. (Again, you don't have to use IDEA, it's just a suggestion) Fill in all the necessary project details as you would normally.

![create_project](/img/posts/flywayGradle/create_project.png "Create Project")

# Create the Gradle Build Script

In our `build.gradle` for our `plugins` sections, we will be using the Java and the Flyway plugins.

    plugins {
        id "java"
        id "org.flywaydb.flyway" version "4.2.0"
    }

We will be using mavenCentral and a local maven repository for our dependencies. The reason for using the local maven repository is because the jdbc driver from Oracle is not publicly accessible. We will have to download the driver from Oracle and install it in our local maven repo, which we'll do in a moment.
    
    repositories {
        mavenCentral()
        mavenLocal()
    }
    
Next we declare our dependency on the jdbc driver.

    dependencies {
        compile ("com.oracle:ojdbc6:11.2.0.4")
        testCompile group: 'junit', name: 'junit', version: '4.12'
    }
    
Finally, we declare some flyway properties like url, driver, etc.

    flyway {
        url = 'jdbc:oracle:thin:@//{host}:{port}/{SID}'
        driver = 'oracle.jdbc.driver.OracleDriver'
        user = {user}
        password = {password}
        table = 'schema_version'
        baselineOnMigrate=true
    }
    
* The `url` property is the connection string to your Oracle database.
* The `table` property is the name of the table that will hold the metadata.
* The `baselineOnMigrate` property dictates whether or not to take a baseline of the schema when migrating.

# Setup the Oracle JDBC Driver

* Download and install [Maven `3.5.0`](https://maven.apache.org/download.cgi). 
* You can download the [Oracle JDBC Driver](http://www.oracle.com/technetwork/database/enterprise-edition/jdbc-112010-090769.html) from this link.

We have to install the JDBC driver to our local maven repository because Oracle's licensing prohibits the driver from being available publicly. The follow command will install this in the local repo. 

`mvn install:install-file -Dfile="{path/to/ojdbc6.jar}" -DgroupId="com.oracle"  -DartifactId="ojdbc6" -Dversion="11.2.0.4" -Dpackaging="jar" -DgeneratePom="true"`

# Create a SQL script

By default the Flyway plugin for Gradle expects your SQL script in this directory structure: ```src/main/resources/db/migration/```. By default it also expects the filename to be something like this: ```V1__Create_person_table.sql```. Let's create the script with the following SQL code.

     create table PERSON (
        ID int not null,
        NAME varchar(100) not null
     );

# Execute the gradle script

Now simply run the gradle script and Flyway will execute our SQL code.

     gradle flywayMigrate -i 

If all is well you'll notice in the output:

     Creating Metadata table: "APPS"."schema_version"
     Migrating schema "APPS" to version 1 - Create person table
     Successfully applied 1 migration to schema "APPS (execution time 00:00.071s).

# Adding Another Migration

Let's insert some records to our ```PERSON``` table we just created by writing a script called ```V2__Add_people.sql```.

     insert into PERSON (ID, NAME) values (1, 'Kirk');
     insert into PERSON (ID, NAME) values (2, 'Spock');
     insert into PERSON (ID, NAME) values (3, 'Scotty');
     
Execute the script:

     gradle flywayMigrate -i
     
Now you'll see:

     Currect version of schema "APPS": 1
     Migrating schema "APPS" to version 2 - Add people
     Sucessfully applied 1 migration to schema "APPS" (execution time 00:00:071s).
     
# Summary

I hope you enjoyed this short description on how to handle Gradle, Flyway, and Oracle databases. This isn't the only way to approach database deployments and this may not be the best solution for your purposes. But I hope this at least helps you think about and experiment with automating your database deployments. 