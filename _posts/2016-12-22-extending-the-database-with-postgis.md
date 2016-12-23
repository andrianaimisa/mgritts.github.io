---
layout: post
title: "telemetR: Extending with PostGIS"
comments: true
tags:
  - postgresql
  - movement ecology
  - telemetR
---

The second post in the telemetR series, extending the database with PostGIS .<!--more-->

In the last post we laid the foundation for an animal telemetry data management system. In this post we will extend the functionality
of the PostgreSQL using the spatial extension PostGIS. PostGIS is an extenstion to the PostgreSQL that allows GIS objects to be stored in the database. We will be able to us PostGIS to store locations as geographic or geometric coordinates and perform many geographic calculations, that would other wise be implemented in R or a GIS software, in postgres.

* toc
{: toc}

## Install PostGIS

On a Mac the easiest way to install PostGIS is to use the [Postgres.app][1] application. This comes packaged with PostGIS. I installed PostgeSQL and PostGIS with homebrew. The nice thing about homebrew is that it'll install or update all the other dependancies as well. During my first attempt I needed to update my version of postgres to 9.6.1. Once each of them is installed create the PostGIS extension in the database you're working on.

{% highlight bash %}
$ psql telemetry  # connect to telemetry database
=# CREATE EXTENSION postgis;

# test the installation worked
=# SELECT PostGIS_Version();

# returns
2.3 USE_GEOS=1 USE_PROJ=1 USE_STATS=1
{% endhighlight %}


## Exploring Spatial Functions

Before applying spatial utilities to our current data, lets explore some of the spatial functions. All SQL function that you should enter will be prefaced with `=#`, anything that is returned by the database will have no leading characters.

### Creating points

{% highlight bash %}
-- sql command
=# SELECT ST_MakePoint(11.001, 46.001) AS point;

-- database return
                 point                    
--------------------------------------------
01010000008D976E1283002640E3A59BC420004740
{% endhighlight %}

This point doesn't look much like a point. We can change how the point is represented. [`ST_AsEWKT` returns][3] the Well-Known Text representation of the geometry.

{% highlight bash %}
=# SELECT ST_AsEWKT(ST_MakePoint(11.001, 46.001)) AS point;

        point         
----------------------
POINT(11.001 46.001)
{% endhighlight %}

We can set the coordinate reference system (CRS) to the point with [`ST_SetSRID`.][4]

{% highlight bash %}
=# SELECT ST_AsEWKT(ST_SetSRID(ST_MakePoint(11.001, 46.001), 4326)) As point;

            point              
--------------------------------
SRID=4326;POINT(11.001 46.001)
{% endhighlight %}    

If we don't want the CRS metadata displayed use [`ST_AsText`][5]

{% highlight bash %}
=# SELECT ST_AsText(ST_SetSRID(ST_MakePoint(11.001, 46.001), 4326)) As point;

point         
----------------------
POINT(11.001 46.001)
{% endhighlight %}

To project the point in a different reference system:

{% highlight bash %}
=# SELECT
    ST_X(
      ST_Transform(
        ST_SetSRID(ST_MakePoint(11.001, 46.001), 4326), 32632)
        )::integer AS x_utm32,
    ST_Y(
      ST_Transform(
        ST_SetSRID(ST_MakePoint(11.001, 46.001), 4326), 32632)
        )::integer AS y_utm32;

 x_utm32 | y_utm32
---------+---------
  654938 | 5096105
{% endhighlight %}

### GPS to Spatial Objects

With these basic function from PostGIS we can start using these tools in our telemetry database. First we will create a new column, `geom` to store the spatial objects, then add data to that column.

{% highlight sql %}
ALTER TABLE telemetry
ADD COLUMN geom geometry(Point, 4326);

UPDATE telemetry
SET geom = ST_SetSRID(ST_MakePoint(longitude, latitude), 4326)
WHERE latitude IS NOT NULL AND longitude IS NOT NULL;

\d telemetry
SELECT longitude, latitude, ST_AsEWKT(geom) AS geom FROM telemetry LIMIT 10;
{% endhighlight %}

Check the schema of the `telemetry` table. There is new column called `geom` that is of type `geometry(Point, 4326)`. Run the SELECT query to see the data. Note that `ST_AsEWKT` is required in order to view the actual points.

Now we can play with the data a little bit. First, we will check how many points each animal has in the `telemtry` table. I've also counted the number in the `geom` field to see how many blank records there are. In this case there are zero. It is possible to have records that don't have coordinates. This can happen when the GPS device is supposed to send a record but doesn't get a fix, or the point is an error and the coordinates have been deleted. There can be many records with no points depending on the terrain and canopy cover.

{% highlight sql %}
SELECT animal_id, count(animal_id) AS total_records, count(geom) AS n_points
FROM telemetry
GROUP BY animal_id
ORDER BY animal_id;

SELECT animal_id, ST_AsText(ST_Centroid(ST_Collect(geom))) AS centroid
FROM telemetry
WHERE geom IS NOT NULL
GROUP BY animal_id
ORDER BY animal_id;
{% endhighlight %}

In the second SELECT statement we calculate the centroid of the points with [`ST_Centroid`][6]. The centroid is geometric center of all the points and is calculated by taking the mean of longitude and latitude.<p>

## Visualize Spatial Data

There are several methods for visualizing spatial data. You can use a dedicated GIS system like QGIS or ESRI products (open and closed source respectively), R, a web service like [geojson.io][7], or even GitHub. About a 1.5 years ago GitHub gists introduced mapping capabilities when uploading a GeoJSON. The command below will create a GeoJSON object of all the points for each individual. The GeoJSON portion can be copied (`geo_json` field) and pasted into a gist.

{% highlight sql %}
SELECT SELECT animal_id, ST_AsGeoJSON(ST_Collect(geom)) as geo_json
FROM telemetry
GROUP BY animal_id;
{% endhighlight %}

[This is the GeoJSON object][8] for `animal_id`, which produces this map.

<script src="https://gist.github.com/kissmygritts/f8d2b9e9719f0db537cba39851ee2d96.js"></script>

Pretty cool. We are definitely making progress with the animal movement database.

### Trajectories

It is important to remember that the data we receive from the satellite is a set of spatial-temporal points. From this set of data we can reconstruct a very rough estimate of the animal's actual movement path, the trajectory. We never get a complete record of the animals movement. The resolution of the path depends on the intervals on the collar as well as the biology of the animal.

In the last step we displayed all the collected points of the animal. In this step we will create a trajectory and display that trajectory on a map.

{% highlight sql %}
SELECT ST_AsGeoJSON(ST_Collect(traj)) AS trajectory
FROM (
  SELECT ST_MakeLine(geom ORDER BY acq_time) AS traj
  FROM telemetry
  GROUP BY animal_id) AS mls;
{% endhighlight %}

The result is a very poorly scaled map. These two animals occur on opposite sides of Nevada. In order to see their trajectories zoom into Lake Tahoe on the West side of Nevada, it is just West of the Minden map marker. The second trajector is on the East side of the state North of White Rock Range Wildlerness Northeast of the Pioche map marker. (sorry for the choice of animals).

<script src="https://gist.github.com/kissmygritts/17daefcc250585317c46940f3f8b7ca4.js"></script>

### Home Ranges

A [home range][9] is defined as the area that an individual spends a majority of its time searching for resources (food, water, mates, etc.). The utilization distribution is a probability density function that predicts the probability of finding an animal in a given area. The home range and utilization distribution generally associated with one another in movement ecology. There are several methods to estimate the home range, for now we will estimate the minimum convex polygon [^1] from each animal in the database. In a future post we will use R to estimate home ranges with other methods.

{% highlight sql %}
SELECT
  animal_id,
  ST_AsGeoJSON(ST_ConvexHull(ST_Collect(geom))::geometry(Polygon, 4326)) AS homerange
FROM telemetry
GROUP BY animal_id;
{% endhighlight %}

Each animal's MCP home range estimation.

<script src="https://gist.github.com/kissmygritts/509b2d95540950347ec9a8087048d7df.js"></script>

<script src="https://gist.github.com/kissmygritts/5b7ec393980dbc073e0e3e99978073b1.js"></script>

## Use PostGIS in the Data Flow

Recall from the last post that we automated the flow of GPS data from `raw_gps` to the `telemetry` table with triggers. We can write triggers automate the creation of spatial objects in the database and help with data QA/QC.

### Spatial Triggers

Instead of creating the `geom` field by hand every time we upload new animals lets create a trigger that automates this process.

{% highlight sql %}
CREATE OR REPLACE FUNCTION create_geom()
RETURNS trigger AS
$BODY$
DECLARE
  thegeom geometry;
BEGIN

IF NEW.longitude IS NOT NULL AND NEW.latitude IS NOT NULL THEN
  thegeom = ST_SetSRID(ST_MakePoint(NEW.longitude, NEW.latitude), 4326);
  NEW.geom = thegeom;
END IF;

RETURN NEW;
END;
$BODY$
LANGUAGE plpgsql VOLATILE
COST 100;

COMMENT ON FUNCTION create_geom()
IS 'When a new record is added to create spatial object "geom" if longitude and latitude is not null';

CREATE TRIGGER add_geom
BEFORE INSERT
ON telemetry
FOR EACH ROW
EXECUTE PROCEDURE create_geom();
{% endhighlight %}

Now lets test it out by deleting one of the animals already in the `telemetry` table then upload the data for that animal again. This will cause the trigger to insert data from the `raw_gps` table into the `telemetry` and the trigger to insert `telemetry.longitude` and `telemetry.latitude` into the `telemetry.geom` as a point.

{% highlight sql %}
DELETE  
FROM telemetry
WHERE animal_id = 1;

COPY raw_gps (serial_num, acq_time, longitude, latitude)
FROM '/Users/mitchellgritts/Dropbox/Data/telemetr/post-data/gps4416.csv'
WITH (FORMAT csv, DELIMITER ',', HEADER true);

SELECT
  animal_id,
  ST_AsText(geom) AS geom
FROM telemetry
WHERE animal_id = 1;
{% endhighlight %}

## Wrap Up

In this post we extended the telemetry database with PostGIS. The database has most of the major functionality we need. The new GPS data is entered into the `raw_gps` table, then automatically inserted into the `telemetry` table. The spatial column `telemtry.geom` is automatically when new records are added. There are many spatial functionalities that can be added to the database. We will get to that in a later post.

In the next post we will connect to the database with R for processing raw data files from the vendors, creating and inserting records into the datbase, and some basic movement analysis.

## Telemetr Series

1. [Introduction][11]
2. [Creating an Animal Movement Database][12]
3. *[Extending the Database with PostGIS][13]*
4. Connecting to the Database with R
5. Adding More SQL Functionality
6. Shiny Web Application
6. A Simple RESTful API
7. ... more

## Footnotes

[^1]: I've written about [MCP here][10].

[1]: http://postgresapp.com/
[3]: http://www.postgis.net/docs/ST_AsEWKT.html
[4]: http://www.postgis.net/docs/ST_SetSRID.html
[5]: http://www.postgis.net/docs/ST_AsText.html
[6]: http://postgis.net/docs/ST_Centroid.html
[7]: geojson.io
[8]: https://gist.github.com/kissmygritts/f8d2b9e9719f0db537cba39851ee2d96?short_path=4d4788b
[9]: https://en.wikipedia.org/wiki/Home_range
[10]: {% post_url 2016-04-02-homerange-mcp %}
[11]: {% post_url 2016-12-19-telemetr-intro %}
[12]: {% post_url 2016-12-20-creating-an-animal-movement-database %}
[13]: {% post_url 2016-12-22-extending-the-database-with-postgis %}
