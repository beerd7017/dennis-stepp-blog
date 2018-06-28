+++
title = "Geocoding Address Information: Part 2"
description = "This is a guide of how approached the process of geocodeing address information."
date = "2018-06-29"
categories = ['Programming', 'Geocode']
tags = ['Geocode', 'Console', 'C Sharp', 'Dapper', 'Linq', 'XML']
thumbnail = "img/posts/geocode/geocode2.jpg"
draft = true
+++

As mentioned in my previous post the eventual goal is to geocode address information from a couple of tables within an ERP database. Real time update was not a concern so I created a console application in C# that could be scheduled as a windows task.

For obtaining the Geocode information I used [Microsoft/BingMapsRESTToolkit](https://github.com/Microsoft/BingMapsRESTToolkit) which is available as a [nuget package](https://www.nuget.org/packages/BingMapsRESTToolkit/). In order to use the api you must [acquire a key](https://msdn.microsoft.com/en-us/library/ff428642.aspx).

To start things off let's just put together a quick proof of concept.

Create a new blank C# console application and add the [BingMapsRESTToolkit](https://github.com/Microsoft/BingMapsRESTToolkit) to the project. In our `App.config`, let's add key for our Bing Map's API key:


Connect to Bing
Connect to Database
Look at DataModel
Build repository
Process the entity and write XML file
Submit the batch to Bing
Parse the XML response back
Write to the database


