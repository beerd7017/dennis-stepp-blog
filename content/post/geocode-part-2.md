+++
title = "Geocoding Address Information: Part 2"
description = "This is a guide of how I approached the process of geocodeing address information."
date = "2018-06-29"
categories = ['Programming', 'Geocode']
tags = ['Geocode', 'Console', 'C Sharp', 'Dapper', 'Linq', 'XML']
thumbnail = "img/posts/geocode/geocode2.jpg"
draft = false
+++

As mentioned in my previous post the eventual goal is to geocode address information from a couple of tables within an ERP database. Real time update was not a concern so I created a console application in C# that could be scheduled as a windows task.

For obtaining the Geocode information I used [Microsoft/BingMapsRESTToolkit](https://github.com/Microsoft/BingMapsRESTToolkit) which is available as a [nuget package](https://www.nuget.org/packages/BingMapsRESTToolkit/). In order to use the api you must [acquire a key](https://msdn.microsoft.com/en-us/library/ff428642.aspx).

## tl;dr

You can find this project on [GitHub](https://github.com/destepp11/GeospatialService.git)

# Creating a database 

Let's new up a database with a couple tables, views, and stored procedures.

Create a customer table.

    create table erp.Customer
    (
      CustId   varchar(10)      not null,
      CustNum  int              not null,
      Address varchar(50)      not null,
      City     varchar(50)      not null,
      State    varchar(50)      not null,
      Zip      varchar(10)      not null,
      Country  varchar(50)      not null,
      Longitude float,
      Latitude float, 
      Geocoded bit default 0,
      ForeignSysRowID uniqueidentifier not null
    )

Create a storefront table.

    create table erp.StoreFront
        (
          StoreFrontId   varchar(10)      not null,
          StoreNum  int              not null,
          Address varchar(50)      not null,
          City     varchar(50)      not null,
          State    varchar(50)      not null,
          Zip      varchar(10)      not null,
          Country  varchar(50)      not null,
          Longitude float,
          Latitude float, 
          Geocoded bit default 0,
          ForeignSysRowID uniqueidentifier not null
        )
        
Create a customer view.

    create view dbo.customer as 
        select CustId, CustNum, Address, City, State, Zip, Country
        from customer

Create a storefront view.

    create view dbo.storefront as 
        select StoreFrontId, StoreNum, Address, City, State, Zip, Country
        from customer

Use dynamic sql and create a stored procedure to get either customer or storefront records.

    CREATE PROCEDURE dbo.sp_GeoGetEntities
        @PassedTableName varchar(255),
    	@NumberOfRows varchar (255)
    AS
      DECLARE @sql nvarchar(255)
    
      set @sql = 'SELECT TOP ' + @NumberOfRows + ' Address, City, State, Zip, Country, ForeignSysRowId from dbo.' +
                 @PassedTableName + ' WHERE Geocoded=0'
      EXECUTE sp_executesql @sql

Use dynamic sql and create a stored procedure to update the geocode information for either customer or storefront records.

    CREATE PROCEDURE dbo.sp_GeoUpdateGeocode
        @PassedTableName varchar(255),
        @Latitude      double precision,
        @Longitude     double precision,
        @ForeignSysRowID uniqueidentifier
    AS
      DECLARE @sql nvarchar(255)
      set @sql = 'UPDATE erp.' + @PassedTableName +
                 ' SET Latitude = @Latitude, Longitude = @Longitude, Geocoded_c = 1 WHERE ForeignSysRowID = @ForeignSysRowID'
      EXEC sp_executesql @sql,
                         N'@Latitude double precision,@Longitude double precision, @ForeignSysRowID uniqueidentifier',
                         @Latitude = @Latitude, @Longitude = @Longitude,  @ForeignSysRowID = @ForeignSysRowID

Use dynamic sql and create a stored procedure to update the geocode flag for either customer or storefront records.

    CREATE PROCEDURE dbo.sp_GeoUpdateFlag
        @PassedTableName varchar(255),
        @ForeignSysRowID uniqueidentifier
    AS
      DECLARE @sql nvarchar(255)
      set @sql = 'UPDATE erp.' + @PassedTableName + ' SET Geocoded = 1 WHERE ForeignSysRowID = @ForeignSysRowID'
      EXECUTE sp_executesql @sql, N'@ForeignSysRowID uniqueidentifier', @ForeignSysRowID = @ForeignSysRowID
    

I'll leave it to you to create some customer and storefront records.

Create a new blank C# console application and add the [BingMapsRESTToolkit](https://github.com/Microsoft/BingMapsRESTToolkit) to the project. In our `App.config`, let's add key for our Bing Map's API key. And while we're here let's add a connection string to our database too.

`App.config`

    <appSettings>
        <add key="BingMapsKey" value="<INSERT-YOUR-KEY>"/>
    </appSettings>
    <connectionStrings>
        <add name="Local" providerName="System.Data.SqlClient" connectionString="<INSERT-YOUR-CONNECTION>" />
    </connectionStrings>


# Data Model

In this example we are going to fetch our data from two view objects and write data to two tables in the database, but we're going to obfuscate this by calling one stored procedure for each operation. With the help of [Dapper](https://www.nuget.org/packages/Dapper/), we can do this in just a few lines. <- I think I said that right. :|

Let's create two enums to represent our tables and views and let's put them in a directory in our project called `Model`:

    public enum DbTable
        {
            Customer,
            Storefront
        }

And the DbView enum:

    public enum DbView
        {
            CustomerView,
            StorefrontView
        }

Luckily for us the data we're interested in for both these tables and view is exactly the same. So we can create a generic model for both Customers and storefronts. I'll call this class `Entity`.
    
    using System;
    using BingMapsRESTToolkit;
    
    namespace GeocodeBatch.Model
    {
        public class Entity
        {
            public int Id { get; set; }
            public string Address { get; set; }
            public string City { get; set; }
            public string State { get; set; }
            public string Zip { get; set; }
            public string Country { get; set; }
            public double Latitude { get; set; }
            public double Longitude { get; set; }
            public SimpleAddress SimpleAddress { get; set; }
            public string Confidence { get; set; }
            public Guid ForeignSysRowID { get; set; }
        }
    }

# Data Access

With the model in place we can now start plumbing our data access. It seems to me to be common to use the repository pattern so let's create an interface for our data repository. There's three things we wish to do:

1. We need to Get some data by passing the `DbView` and the number of records we want to pull `rowCount` and create a `List<Entity>` that we can send off to Bing's services later on.
2. After we get the geocode info we'll need to update the database by providing `Entity` and `DbTable`.
3. In cases where we did not get geocode information or we're not willing to accept it because of concerns of accuracy, we need to mark these records as we attempt them so that we don't pick them up for reprocessing over and over again.

So we have three methods to define in our interface as follows.

    using System.Collections.Generic;
    using GeocodeBatch.Model;
    
    namespace GeocodeBatch.Data
    {
        internal interface IRepository
        {
            List<Entity> Get(DbView viewName, string rowCount);
            void Geocode(Entity entity, DbTable table);
            void UpdateGeocodeFlag(Entity entity, DbTable table);
        }
    }
    
Now let's implement this interface. Create a new class called `EntityRepository` which inherits from `IRepository` and go ahead and stub out the three methods.

    using System.Collections.Generic;
    using GeocodeBatch.Model;
    
    namespace GeocodeBatch.Data
    {
        public class EntityRepository : IRepository
        {
            public List<Entity> Get(DbView viewName, string rowCount)
            {
                throw new System.NotImplementedException();
            }
    
            public void Geocode(Entity entity, DbTable table)
            {
                throw new System.NotImplementedException();
            }
    
            public void UpdateGeocodeFlag(Entity entity, DbTable table)
            {
                throw new System.NotImplementedException();
            }
        }
    }

Create a `private readonly string _connectionString` and a constructor so that we can eventually new up this class in our main program.

      public EntityRepository(string connectionString)
            {
                _connectionString = connectionString;
            }

And let's create some constants to represent our store procedures in our database:

    private const string GetEntities = "sp_GeoGetEntities";
    private const string UpdateGeocode = "sp_GeoUpdateGeocode";
    private const string UpdateFlag = "sp_GeoUpdateFlag";
    
Now let's implement the `Get` method. Remember to add the Dapper nuget package if you haven't already.

      public List<Entity> Get(DbView viewName, string rowCount)
            {
                List<Entity> entities;
                using (IDbConnection db = new SqlConnection(_connectionString))
                {
                    var p = new DynamicParameters();
                    p.Add("@PassedTableName", viewName.ToString());
                    p.Add("@NumberOfRows", rowCount);
                    entities = (List<Entity>) db.Query<Entity>(GetEntities, p, commandType: CommandType.StoredProcedure);
                }
    
                return entities;
            }
            
And now the `Geocode` method:

    public void Geocode(Entity entity, DbTable table)
            {
                using (IDbConnection db = new SqlConnection(_connectionString))
                {
                    var p = new DynamicParameters();
                    p.Add("@PassedTableName", table.ToString());
                    p.Add("@Latitude", entity.Latitude);
                    p.Add("@Longitude", entity.Longitude);
                    p.Add("@ForeignSysRowID", entity.ForeignSysRowID);
                    db.Execute(UpdateGeocode, p, commandType: CommandType.StoredProcedure);
                }
            }
            
And lastly `UpdateGeocodeFlag`:

    public void UpdateGeocodeFlag(Entity entity, DbTable table)
            {
                using (IDbConnection db = new SqlConnection(_connectionString))
                {
                    var p = new DynamicParameters();
                    p.Add("@PassedTableName", table.ToString());
                    p.Add("@ForeignSysRowId", entity.ForeignSysRowID);
                    db.Execute(UpdateFlag, p, commandType: CommandType.StoredProcedure);
                }
            }

# Main program flow

In our main program, let's add the connection strings and the EntityRepository and start in on our program's flow.

    private static readonly string ConnectionString =
                ConfigurationManager.ConnectionStrings["DefaultConnection"].ConnectionString;
            private static readonly EntityRepository EntityRepository = new EntityRepository(ConnectionString);
            
            private static void Main(string[] args)
            {
                var entitiesToProces = args[0];
    
                if (int.Parse(entitiesToProces) > 2500)
                {
                    Console.WriteLine("Due to API restrictions we cannot process more than 2,500 rows per job.");
                }
            }
            
Let's make a private method for getting the list of entities and setting their id. We want to set their id because when the results come back from Bing they will be in numeric sequence starting at 1. As we assign the id were going to hand them over to a helper class that will put the Entity into XML format.

    private static void GeocodeEntity(DbView view, DbTable table, string count)
            {
                var initialId = 1;
                var entities = new List<Entity>(EntityRepository.Get(view, count));
                if (entities.Count <= 0) return;
                
                entities.ForEach(e =>
                {
                    e.Id = initialId++;
                    BatchWriter.AddEntity(e);
                });
            }

# Writing a Xml for batch processing

Let's fix the compile error and create a `BatchWriter` class in a new directory we'll call `Services`. We'll need to define a couple private variables for our XML namespace and the root of our XML document. Also we need a  `SimpleAddress` object for Bing and finally adding the elements and attributes to the XML format Bing expects. We should also create a method to actually save this file so we can send it later on.

    using System.Configuration;
    using System.Xml.Linq;
    using AGI.GeocodeBatch.Model;
    
    namespace AGI.GeocodeBatch.Services
    {
        public class BatchWriter
        {
            private static readonly XNamespace Namespace = "http://schemas.microsoft.com/search/local/2010/5/geocode";
            private static readonly XElement Root = new XElement(Namespace + "GeocodeFeed", new XAttribute("Version", "2.0"));
    
            public static void AddEntity(Entity entity)
            {
                var epicorAddress = new EpicorAddress(entity);
                entity.SimpleAddress = AddressService.CreateSimpleAddress(epicorAddress);
    
                Root.Add(
                    new XElement(Namespace + "GeocodeEntity",
                        new XAttribute("Id", entity.Id),
                        new XAttribute("xmlns", Namespace),
                        new XElement("GeocodeRequest",
                            new XAttribute("Culture", "en-US"),
                            new XAttribute("IncludeNeighborhood", "1"),
                            new XElement("Address",
                                new XAttribute("AddressLine", entity.SimpleAddress.AddressLine),
                                new XAttribute("AdminDistrict", entity.SimpleAddress.AdminDistrict),
                                new XAttribute("Locality", entity.SimpleAddress.Locality),
                                new XAttribute("PostalCode", entity.SimpleAddress.PostalCode))))
                );
            }
    
            public static void WriteFile()
            {
                Root.Save(ConfigurationManager.AppSettings.Get("XmlOutData"));
            }
        }
    }
    
We're going to add another key to our `App.config` for the file name of the XML document.

    <add key="XmlOutData" value ="locationInfo.xml"/>
    
# Submitting the batch to Bing
    
Back in our main program let's write the XML file and create a method to submit the batch.

    private static void GeocodeEntity(DbView view, DbTable table, string count)
            {
                var initialId = 1;
                var entities = new List<Entity>(EntityRepository.Get(view, count));
                if (entities.Count <= 0) return;
                
                entities.ForEach(e =>
                {
                    e.Id = initialId++;
                    BatchWriter.AddEntity(e);
                });
                
                BatchWriter.WriteFile();
                SubmitBatch();
            }
    
            private static void SubmitBatch()
            {
               
                
            }
            
To implement the `SubmitBatch` method let's build ourselves a service class that will handle a brunt of the work. Declare a few private strings and a constructor like this:

        private static string _dataFormat = "xml";
        private static string _dataFilePath = Directory.GetCurrentDirectory() + ConfigurationManager.AppSettings.Get("XmlOutPath");
        private static string _bingMapsKey = ConfigurationManager.AppSettings.Get("BingMapsKey");
    
        public BatchService(string dataFilePath, string dataFormat, string key)
        {
            _dataFilePath = dataFilePath;
            _dataFormat = dataFormat;
            _bingMapsKey = key;
        }
        
Now we need a method to create the job for Bing. Mostly this is a copy/paste from there documentation.

    public static string CreateJob()
            {
                const string contentType = "application/xml";
                var queryStringBuilder = new StringBuilder();
                queryStringBuilder.Append("input=").Append(Uri.EscapeUriString(_dataFormat));
                queryStringBuilder.Append("&");
                queryStringBuilder.Append("key=").Append(Uri.EscapeUriString(_bingMapsKey));
    
    
                //Build the HTTP URI that will upload and create the geocode dataflow job
                var uriBuilder =
                    new UriBuilder(ConfigurationManager.AppSettings.Get("BingUri"))
                    {
                        Path = ConfigurationManager.AppSettings.Get("BingUriPath"),
                        Query = queryStringBuilder.ToString()
                    };
    
                //Include the data to geocode in the HTTP request
                using (var dataStream = File.OpenRead(_dataFilePath))
                {
                    var request = (HttpWebRequest) WebRequest.Create(uriBuilder.Uri);
                    request.Method = "POST";
                    request.ContentType = contentType;
    
                    using (var requestStream = request.GetRequestStream())
                    {
                        var buffer = new byte[16384];
                        var bytesRead = dataStream.Read(buffer, 0, buffer.Length);
                        while (bytesRead > 0)
                        {
                            requestStream.Write(buffer, 0, bytesRead);
                            bytesRead = dataStream.Read(buffer, 0, buffer.Length);
                        }
                    }
    
                    using (var response = (HttpWebResponse) request.GetResponse())
                    {
                        if (response.StatusCode != HttpStatusCode.Created)
                        {
                            var ex = new Exception(
                                "An HTTP error status code was encountered when creating the geocode job.");
                            throw ex;
                        }
    
                        var dataflowJobLocation = response.GetResponseHeader("Location");
                        if (string.IsNullOrEmpty(dataflowJobLocation))
                        {
                            var ex = new Exception(
                                "The 'Location' header is missing from the HTTP response when creating a goecode job.");
                            throw ex;
                        }
    
                        return dataflowJobLocation;
                    }
                }
            }

We'll need a method to check the results and another helper class as well. Let's create the helper class in `Services` and call it `DownloadDetails`.

    namespace GeocodeBatch.Services
       {
           public class DownloadDetails
           {
               public string JobStatus { get; set; }
               public string Suceededlink { get; set; }
               public string Failedlink { get; set; }
           }
       }
       
Now we can copy/paste some more code from Bing's documentation for the check status method.

    public static DownloadDetails CheckStatus(string dataflowJobLocation)
            {
                var statusDetails = new DownloadDetails { JobStatus = "Pending" };
                var uriBuilder = new UriBuilder(dataflowJobLocation + @"?key=" + _bingMapsKey + "&output=xml");
                var request = (HttpWebRequest)WebRequest.Create(uriBuilder.Uri);
    
                request.Method = "GET";
    
                using (var response = (HttpWebResponse)request.GetResponse())
                {
                    if (response.StatusCode != HttpStatusCode.OK)
                    {
                        var ex = new Exception("An HTTP error status code was encountered when checking job status.");
                        throw ex;
                    }
    
                    using (var receiveStream = response.GetResponseStream())
                    {
                        if (receiveStream == null) return (statusDetails);
                        var reader = new XmlTextReader(receiveStream);
                        while (reader.Read())
                        {
                            if (!reader.IsStartElement()) continue;
                            if (reader.Name.Equals("Status"))
                            {
                                //return job status
                                statusDetails.JobStatus = reader.ReadString();
                                return (statusDetails);
                            }
                            else if (reader.Name.Equals("Link"))
                            {
                                reader.MoveToFirstAttribute();
                                if (!reader.Value.Equals("output")) continue;
                                reader.MoveToNextAttribute();
                                if (reader.Value.Equals("succeeded"))
                                {
                                    statusDetails.Suceededlink = reader.ReadString();
    
                                }
                                else if (reader.Value.Equals("failed"))
                                {
                                    statusDetails.Failedlink = reader.ReadString();
                                }
                            }
                        }
                    }
                }
                return (statusDetails);
            }

            
We'll also need a method to download the results, again this is a copy/paste from Bing's documentation for the most part.
            
   public static void DownloadResults(DownloadDetails statusDetails)
           {
               if (statusDetails.Suceededlink != null && !statusDetails.Suceededlink.Equals(String.Empty))
               {
                   var successUriBuilder = new UriBuilder(statusDetails.Suceededlink + @"?key=" + _bingMapsKey);
                   var request1 = (HttpWebRequest)WebRequest.Create(successUriBuilder.Uri);
   
                   request1.Method = "GET";
   
                   using (var response = (HttpWebResponse)request1.GetResponse())
                   {
                       if (response.StatusCode != HttpStatusCode.OK)
                       {
                           var ex = new Exception("An HTTP error status code was encountered when downloading results.");
                           throw ex;
                       }
   
                       using (var receiveStream = response.GetResponseStream())
                       {
                           var successfile = new StreamWriter("Success.xml");
                           if (receiveStream != null)
                               using (var r = new StreamReader(receiveStream))
                               {
                                   string line;
                                   while ((line = r.ReadLine()) != null)
                                   {
                                       successfile.WriteLine(line);
                                   }
                               }
                           successfile.Close();
                       }
                   }
               }
   
   
               if (statusDetails.Failedlink == null || statusDetails.Failedlink.Equals(string.Empty)) return;
               {
                   var failedUriBuilder = new UriBuilder(statusDetails.Failedlink + @"?key=" + _bingMapsKey);
                   var request2 = (HttpWebRequest)WebRequest.Create(failedUriBuilder.Uri);
   
                   request2.Method = "GET";
   
                   using (var response = (HttpWebResponse)request2.GetResponse())
                   {
                       if (response.StatusCode != HttpStatusCode.OK)
                       {
                           var ex = new Exception("An HTTP error status code was encountered when downloading results.");
                           throw ex;
                       }
   
                       using (var receiveStream = response.GetResponseStream())
                       {
                           var failedfile = new StreamWriter("Failed.xml");
                           if (receiveStream != null)
                               using (var r = new StreamReader(receiveStream))
                               {
                                   string line;
                                   while ((line = r.ReadLine()) != null)
                                   {
                                       failedfile.WriteLine(line);
                                   }
                               }
                           failedfile.Close();
                       }
                   }
               }
           }
            
Now the `BatchService` is built we can head back over to our main program and implement the `SubmitBatch` method. We'll create the job and query the status of the job every 30 seconds. Once the status is not "Pending" we'll download the results.

    private static void SubmitBatch()
            {
                var dataflowJobLocation = BatchService.CreateJob();
    
                //Continue to check the dataflow job status until the job has completed
                DownloadDetails statusDetails;
                do
                {
                    statusDetails = BatchService.CheckStatus(dataflowJobLocation);
                    if (statusDetails.JobStatus == "Aborted")
                    {
                        Log.LogError("Job was aborted due to an error.");
                        throw new Exception("Job was aborted due to an error.");
                    }
    
                    Thread.Sleep(30000); //Get status every 30 seconds
                }
                while (statusDetails.JobStatus.Equals("Pending"));
                // Bing GeoSpatialData Service may produce Success.xml and Failed.xml
                BatchService.DownloadResults(statusDetails);
            }

# Parsing the results

All right. Cool. Now we need to read these results and update the database accordingly. Let's create a `BatchParser` class in the services directory for this.

To keep the class clean we'll pull the namespace of the xml document from our `App.config` and also create an internal class called `GeoEntity`

`App.config`

    <add key="BingXMLNamespace" value="http://schemas.microsoft.com/search/local/2010/5/geocode"/>

`BatchParser.cs`

    private static readonly XNamespace Namespace = ConfigurationManager.AppSettings.Get("BingXMLNamespace");
            
            internal class GeoEntity
            {
                public int Id;
                public string Confidence;
                public double Latitude;
                public double Longitude;
    
                public GeoEntity(int id, string confidence, double latitude, double longitude)
                {
                    Id = id;
                    Confidence = confidence;
                    Latitude = latitude;
                    Longitude = longitude;
                }
            }

Now we need to parse through the xml and set our longitude, latitude, and confidence values to our entity objects which are keyed by the id field.

    public static Entity GetGeodata(Entity entity)
            {
                var parsedList = new List<GeoEntity>();
    
                parsedList.AddRange(
                    from geoentity in XElement.Load("Success.xml").DescendantsAndSelf(Namespace + "GeocodeEntity")
                    let thisId = (int)geoentity.Attribute("Id")
                    let responses = geoentity.Descendants(Namespace + "GeocodeResponse")
                    from response in responses
                    let thisConf = (string)response.Attribute("Confidence")
                    let points = response.Descendants(Namespace + "Point")
                    from point in points
                    let thisLat = (double)point.Attribute("Latitude")
                    let thisLng = (double)point.Attribute("Longitude")
                    select new GeoEntity(thisId, thisConf, thisLat, thisLng));
    
    
                parsedList.ForEach(g =>
                {
                    if (g.Id != entity.Id) return;
                    entity.Confidence = g.Confidence;
                    entity.Latitude = g.Latitude;
                    entity.Longitude = g.Longitude;
                });
                return entity;
            }
    
Now let's go back to the main program and wire this in and then make a new service to update our database:

    private static void GeocodeEntity(DbView view, DbTable table, string count)
            {
                var initId = 1;
                var entities = new List<Entity>(EntityRepository.Get(view, count));
                if (entities.Count <= 0) return;
    
                entities.ForEach(e =>
                {
                    e.Id = initId++;
                    BatchWriter.AddEntity(e);
                });
    
                BatchWriter.WriteFile();
                SubmitBatch();
    
                entities.ForEach(e =>
                {
                    e = BatchParser.GetGeodata(e);
                    EntityProcessor.UpdateEntity(e, table);
                });
            }

# Processing the entities
            
Create a new class called `EntityProcessor`. We need our connection string and our `EntityRepository` as well. If the zip code is empty we just want to check the flag, otherwise we'll update the database.

    using System.Configuration;
    using GeocodeBatch.Data;
    using GeocodeBatch.Model;
    
    namespace GeocodeBatch.Services
    {
        public class EntityProcessor
        {
            private static readonly string ConnectionString = ConfigurationManager.ConnectionStrings["DefaultConnection"].ConnectionString;
            private static readonly EntityRepository EntityRepository = new EntityRepository(ConnectionString);
            
            public static void UpdateEntity(Entity entity, DbTable table)
            {
                if (entity.Zip == "")
                {
                    EntityRepository.UpdateGeocodeFlag(entity, table);
                }
                else
                {   
                    EntityRepository.Geocode(entity, table);
                }
            }
        }
    }

# Wrapping things up
    
Now to finish things off in the main program, we need to add calls to the `GeocodeEntity` method for `Customer` and `Storefront` at the bottom of our main method.

    GeocodeEntity(DbView.CustomerView, DbTable.Customer, entitiesToProcess);
    GeocodeEntity(DbView.StorefrontView, DbTable.Storefront, entitiesToProcess);
    
And that's a wrap! You can find this project on [GitHub](https://github.com/destepp11/GeospatialService.git). Thanks for following along!