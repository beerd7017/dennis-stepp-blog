+++
title = "2017: Postmortem"
description = "A postmortem analysis of 2017"
date = "2017-12-29"
categories = ['Programming', 'Software Engineering', 'DevOps', 'Design', 'Productivity']
tags = ['Ludum Dare', 'Unity', 'Gradle', 'C#', 'Java', 'Powershell', 'Game Development', 'Design', 'Productivity', 'Programming']
thumbnail = "img/posts/postmortem2017/programmingkid.jpg"
+++

# First Things First
2017 was a great year for me personally and professionally. At work I migrated from the Enterprise Resource Planning team to the Software Development team. I attended a couple conferences here in the Southeast. I participated in 3 game jams and several other projects through the year. I'd like to take some time now to reflect on this year's journey and start thinking about the roadmap for 2018.

I wouldn't have had such a great year had it not been for the support, encouragement, and mentorship of the following people:

*  **Kelsey Cook** - for putting up with my antics, complaints, and frustrations. And for giving me the support to attend conferences and meetups and the time to work on multiple projects throughout the year.

*  **Daniel Pulley** - for the constant stream of creativity which fuels my drive to keep creating and keep solving problems.

*  **Jordan Willis** - for being patient and helpful when I am lost in Unity and have no idea what I'm doing.

*  **Jamie Phillips** - for being an amazing mentor and building up my confidence in my skills. You have made me a much better developer by just telling me that I do know what I'm doing.

*  **Joe Myers** - for being supportive and witness to the gains I've made as a developer. Without your encouragement I wouldn't be where I am today.
 
 ![GolfClap](/img/posts/postmortem2017/golfclap.gif "Golf Clap")
 
# Transitioning to Another Team
 From 2013 to 2017 I've worked at Radio Systems Corporation on the Enterprise Resource Planning team as an Associate Java Developer. I have had various duties added to my plate over these years from development of custom Java web applications on Oracle's Application Framework, credit card processing jobs, various reports using PL/SQL, and finally development and support of our B2B eCommerce system. While working for the ERP team I mostly had to respond to firefighting and fulfilling the request of the business user that was screaming the loudest. As someone who wants to follow procedure and follow best practices in software development this was very taxing on my mind. 
 
 This year was different. In 2017, I was able to shift my focus on real development and push the envelope as far as RSC was concerned. Our B2B eCommerce system is a giant legacy system full of bad decisions and lack of attention to design and best practices. So I set out to develop a new proof of concept to gauge the level of difficulty of replacing the system. I chose to use [Gatsby](https://www.gatsbyjs.org/) because it's static and can leverage web technologies like [React](https://reactjs.org/), which we use already at RSC for a couple of properties. Within a week I was done with the POC which was made of a landing page, login modal, about page, store catalogs, and item detail pages. For the store catalogs and item detail pages, I pulled in JSON objects and rendered them to the screen which was one of the more important concepts I wanted to prove. Hopefully, some decisions will be made in early 2018 regarding this project.
 
 I was able to escape the grasp of our Oracle instance and begin working on a ASP.NET application we have. It felt excellent to build out several features that the users had defined fairly well. This was a very much welcomed breath of fresh air and I really enjoyed working with C#, Ajax, JavaScript, and JQuery.
 
 Automated builds and deployments were also a big initiative for me this year. I was able to produce builds for our custom java applications and our B2B eCommerce system using [Gradle](https://gradle.org/). The main challenge in this was finding the propertary Oracle libraries and dependencies. After I had my build working locally, I setup the projects on our CI server and got the builds working there. Then I setup automated deployments to our Oracle development environment and I'm waiting on approval from the owners of the ERP system to allow Test and Production to be automated. I also automated the database migrations using [Flyway](https://flywaydb.org/), which is also waiting on approval.
 
 I wrapped up 2017 attempting to build a [Docker](https://www.docker.com/) container for the Oracle Enterprise Business Suite. I didn't quite get it finished before the holidays but I sure did run into some real issues trying to get this to work. Most of the issues have to do with container size limitations which is understandable when the Oracle EBS installation files are around 50 GB compressed and 200 GB installed. This is still a work in progress and I hope I can pull it off because this could change the game for our development and DBA teams and allow us to move much faster without holding instances hostage for long Waterfall approach projects.
 
# Attending Conferences and Meetups

I attended two conferences this year. The first one was [CodeStock](http://www.codestock.org/) in Knoxville, TN. This was my third time at this conference and I got distracted by a couple of production issues at work which meant I missed the Keynote and 2 or 3 sessions. Despite that I had a great time at CodeStock and I highly recommend it, plus the sponsors had some cool swag with them. The second conference I attended was [DevSpace](https://www.devspaceconf.com/) in Hunstville, AL. DevSpace stood out to me more than CodeStock for one reason: I hung out with the speakers. Jamie Phillips, was presenting at DevSpace so he introduced me to a few of them after their speakers dinner at the [Old Town Beer Exchange](http://otbxhsv.com/). As we were all walking back one of them asked me what I was speaking about. I told them that I wasn't speaking I was just tagging along with all of them and by the time we were back at the hotel I was convinced that I should prepare an abstract and submit it to a few conferences. Later that week I created my abstract titled "**Impress Your Boss By Sitting On Your Ass: Automated Builds and Deployments**". I've submitted it to a couple conferences so we'll see how that goes ;).

I've also been much more active in the development community by attending various meetups like Agile, Knox.Net, QA, Functional, and DevBeers. Attending these meetups is a great way to network for sure, but it has also helped me have a broader and deeper perspective on problems I have to solve at work and other projects.

# Particpating in Game Jams   

This year I took part in three [Ludum Dare](https://ldjam.com/) game jams. The first one was in April and the theme was **Small World**. I worked with both Jordan and Dan to create a game that was simiular to a snake game in which your had to devour planets to increase the number of segments on your body. I mainly just created the UI menus as this was my first time using Unity for game development. We placed 255th out of 2945 submissions.

![Eat](/img/posts/postmortem2017/eat.png "Eat")

The second jam was in July and the theme was **Running Out of Power**. I worked with Dan and Jordan again to create a puzzle game in which you were a service robot and directive was to restore power to the factory. Again I created the menu but I also created a bunch of the game's story and content. I think with more time and effort there could be a more fully fledged game but we are currently working on another project. We placed 127th out of 2350 submissions.

![Fizzle](/img/posts/postmortem2017/fizzle.png "Fizzle")

The third jam took place in December and the theme was **The More You Have, the Worst It Is**. For this jam, Jordan had to work all weekend so I set off to create everything by myself, except Dan offered to write some music and do some art. I decided I wanted to create a shoot 'em up bullet hell game because I figured the complexity was pretty low for those kind of game, which is true. Because of that there's the expectation that there should be quite a bit of content. I got a lot of good feedback from the LD community and there wasn't anything I was real surprised about other than placing 411th out of 2892 submissions. Dan killed it on the music placing 98th!

![SpaceBaddies](/img/posts/postmortem2017/space.gif "SpaceBaddies")

# Roadmap of 2018

## Things I'll Keep Doing

* Attending conferences and meetups.

* Learning.

* Creating.

* Sharing.

* Bloging.

* Reading.

* Game Development.

## Things I'll Stop Doing

* Not working out.

* Eating out all the time.

* Being negative or non-productive.

## Things I Want to Start Doing.

* Meal planning!

* Learn [Kotlin](https://kotlinlang.org/), [Elm](http://elm-lang.org/), and [PHP](http://php.net/) for a pretty wide variety.

* Contribute to open source projects.

* Recreate the website for the [Earl Park Fall Festival](https://www.earlparkfestival.com/)

* Start development and promoting a project I wish to open source [JAMSpace](https://github.com/destepp11/bluegrass-jam).

Looking forward to the new year and everything it has to offer!





 