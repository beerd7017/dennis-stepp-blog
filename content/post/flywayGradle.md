+++
title = "Flyway, Gradle, Oracle JDBC"
description = "How to setup the Flyway Gradle plugin and connecting to an Oracle Database."
date = "2017-10-11"
categories = ['Automation', 'Programming', 'Database']
tags = ['Gradle', 'Oracle', 'Flyway', 'Maven', 'Productivity']
thumbnail = "img/posts/flywayGradle/gradlephant.png"
+++

I'm writing this blog post to hopefully help out some poor soul who is trying to automate their [Oracle database](http://www.oracle.com/technetwork/database/enterprise-edition/jdbc-112010-090769.html) deployments using the [Flyway](https://flywaydb.org) plugin for [Gradle](https://gradle.org/). This was kind of a pain to accomplish because at the time of this post the documentation was scattered about and difficult to piece together. Let's take care of that. ``</rant>``

# Assumptions
* You're somewhat familiar with how Gradle builds work.
* You're somewhat familiar with Oracle Database.
* You're new to Flyway

# Tools and Tech
* JetBrains IntelliJ IDEA
* Gradle
* Maven
* Flyway
* Oracle Database

File -> New Project

Setup Gradle Build Script

Download Oracle jdbc jar

Setup local maven repo

Add Oracle jdbc jar to local maven repo

Add a .sql script for flyway to migration

Run gradle flywayInfo

Run gradle flywayMigrate