+++
title = "Geocoding Address Information: Part I"
description = "This is a guide of how approached the process of geocodeing address information."
date = "2018-06-28"
categories = ['Programming', 'Geocode']
tags = ['Geocode', 'Console', 'C Sharp']
thumbnail = "img/posts/geocode/geocode.jpg"
draft = false
+++

The eventual goal is to geocode address information from a couple of tables within an ERP database. Real time update was not a concern so I created a console application in C# that could be scheduled as a windows task.

For obtaining the Geocode information I used [Microsoft/BingMapsRESTToolkit](https://github.com/Microsoft/BingMapsRESTToolkit) which is available as a [nuget package](https://www.nuget.org/packages/BingMapsRESTToolkit/). In order to use the api you must [acquire a key](https://msdn.microsoft.com/en-us/library/ff428642.aspx).

To start things off let's just put together a quick proof of concept.

Create a new blank C# console application and add the [BingMapsRESTToolkit](https://github.com/Microsoft/BingMapsRESTToolkit) to the project. In our `App.config`, let's add key for our Bing Map's API key:


    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="BingMapsKey" value="<INSERT-KEY-HERE"/>
        </appSettings>
        <startup> 
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
        </startup>
    </configuration>

Now let's head back over to `Program.cs`. And let's use the [BingMapsRESTToolkit](https://github.com/Microsoft/BingMapsRESTToolkit) to query for all the geocode information that matches with our query of "Knoxville". To do this we'll save the result of the request to Bing as such:

    var request = ServiceManager.GetResponseAsync(new GeocodeRequest()
             {
                 BingMapsKey = System.Configuration.ConfigurationManager.AppSettings.Get("BingMapsKey"),
                 Query = "Knoxville"
             }).GetAwaiter().GetResult();
               
We should check the response from Bing to make sure that neither the response or it's resourceSets are not null and that it contains something. If all is well we can grab the `latitude` and `longitude` from the `Location` in each resource in the set, then simply print this out to the console.

        if (request?.ResourceSets != null &&
              request.ResourceSets.Length > 0 &&
              request.ResourceSets[0].Resources != null &&
              request.ResourceSets[0].Resources.Length > 0)
             {
                 foreach (var resource in request.ResourceSets[0].Resources)
                 {
                     var location = resource as Location;
                     if (location == null) continue;
                     var latitude = location.Point.Coordinates[0];
                     var longitude = location.Point.Coordinates[1];
 
                     Console.WriteLine(location.Name);
                     Console.WriteLine("Latitude is: " + latitude + " Longitude is: " + longitude);
                 }
             }
             else
             {
                 Console.WriteLine("No results found.");
             }

And that's it for our proof of concept. Here's the full `Program.cs`:

    using System;
    using BingMapsRESTToolkit;

    namespace Geoencoding_POC
    {
        internal class Program
        {
            private static void Main(string[] args)
            {
                var request = ServiceManager.GetResponseAsync(new GeocodeRequest()
                {
                    BingMapsKey = System.Configuration.ConfigurationManager.AppSettings.Get("BingMapsKey"),
                    Query = "Knoxville"
                }).GetAwaiter().GetResult();
    
                if (request?.ResourceSets != null &&
                 request.ResourceSets.Length > 0 &&
                 request.ResourceSets[0].Resources != null &&
                 request.ResourceSets[0].Resources.Length > 0)
                {
                    foreach (var resource in request.ResourceSets[0].Resources)
                    {
                        var location = resource as Location;
                        if (location == null) continue;
                        var latitude = location.Point.Coordinates[0];
                        var longitude = location.Point.Coordinates[1];
    
                        Console.WriteLine(location.Name);
                        Console.WriteLine("Latitude is: " + latitude + " Longitude is: " + longitude);
                    }
                }
                else
                {
                    Console.WriteLine("No results found.");
                }
                Console.ReadLine();
            }
        }
    }

The results of the program are:

    Knoxville, TN
    Latitude is: 35.9606781005859 Longitude is: -83.921028137207
    Knoxville, IA
    Latitude is: 41.3202896118164 Longitude is: -93.0998764038086
    Knoxville, IL
    Latitude is: 40.9079704284668 Longitude is: -90.2811889648438
    Knoxville, GA
    Latitude is: 32.724781036377 Longitude is: -83.9962768554688
    Saint-Raymond, QC
    Latitude is: 46.8934211730957 Longitude is: -71.8283004760742
    
In the next post we'll do some real world application where we will geocode an address such as `1600 Pennsylvania Ave NW, Washington, DC 20500` , but this should help get you started.