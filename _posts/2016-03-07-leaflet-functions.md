---
layout: post
title:  "Wrapper functions for Leaflet in R"
tags:
  - R
  - Leaflet
---

Over the last several months I've been building a Shiny app for exploratory analysis and visualization of animal movement data. I want the app to be fully interactive so I decided to use leaflet over other mapping packages. Creating this application is also a good opportunity to write some wrapper functions to make analysis in an interactive session easier for the people that I work with.

<!--more-->

# The data

The data comes from animals tagged with a GPS collar. The collar works like any other GPS device, it connects to GPS satellites and records locations at intermittent time intervals. Depending on the species the intervals are either 12, 7, 8 or 1 hours. The data that we get are xy timestamped data with a few additional fields specifying the accuracy of the location. The data is downloaded from the manufacturers servers and stored in our own location database.

A lot of the data wrangling occurs prior to import to the database. The little bit of data cleaning that occurs in the application is converting the timestamp from a character to class `POSIXct` and adding UTM coordinates to the dataframe.

# Leaflet

Leaflet is an R package that uses the Leaflet Javascript API to make interactive maps in R. A few nuances about the Leaflet package. The documentation for leaflet is really good, [check it out for more information.](https://www.rstudio.github.io/leaflet)

1. You can use any of the `sp` classes to create the map.
2. If there are NA values in the dataframe, leaflet will stop plotting.

# The functions

The first function really only useful for the displaying a map of the data on the front page of the application. The function takes a dataframe as input loops through each unique animal ID (ndowid) to plot each animal's locations and returns a leaflet map.

{% highlight r %}
CollarMap <- function(dataframe) {
  df <- as.data.table(dataframe)
  df <- df[complete.cases(df[, .(long_x, lat_y)])]
  df_lines <- df[, .SD[c(seq(1, .N, 20), .N)], by = ndowid]
  unq_id <- unique(df[, ndowid])
  map <- leaflet() %>% addProviderTiles("Esri.WorldTopoMap")

  for (n in unq_id) {
    d <- df_lines[ndowid == n]
    dp <- d[, .SD[c(1, .N)]]

    map <- addPolylines(map, lng = d$long_x, lat = d$lat_y,
                        weight = 2, color = "black", opacity = .4)
    map <- addCircleMarkers(map, lng = dp$long_x, lat = dp$lat_y,
                            stroke = FALSE, radius = 4, color = "navy",
                            fillOpacity = 1,
                            popup = paste("NDOW ID:", n))
  }
  return(map)
}
{% endhighlight %}

In my application the data is read using the `data.table` package. The `as.data.table` function is used to ensure that the data is in the proper format. As stated above, I remove all NA from the dataframe using the `complete.cases` function call.

The procedure on line 4 uses the `data.table` syntax to subset the data.table and return every 20 records. I do this to simplify the polylines on the leaflet map. Some of these animals have over 10,000 GPS locations, trying to plot all those makes for a messy map. I use the same method to plot only the 1st and last GPS location as circles on line 9. The resulting map looks like this.

![CollarMap map](/assets/collarmap.jpg)
<div class="caption">
  <p class = "caption-text">
    <em>A screen shot from the telemetR Shiny application I developed for NDOW. The CollarMap function is used to generate the leaflet map.</em>
  </p>
</div>

The second function `DeviceMapping` is similar to the first function, and honestly, should be combined with the first function. I've just been to lazy to do that at this point. The major difference is that this function plots every point and gives each animal a unique color. The function also creates a layer control box that allows user to click layers on and off.

{% highlight r %}
DeviceMapping <- function(dataframe, basemap = "Esri.WorldTopoMap") {
  dat <- as.data.table(dataframe)
  dat <- dat[complete.cases(dat[, .(long_x, lat_y)])]
  unique.id <- unique(dat$ndowid)
  pal <- ggthemes::gdocs_pal()(20)

  device.map <- leaflet() %>%
    addProviderTiles(basemap)
  layer.group <- list()

  for(i in 1:length(unique.id)) {
    df <- dat[ndowid == unique.id[i]]
    device.map <- addPolylines(device.map,
                               lng = df$long_x, lat = df$lat_y,
                               group = as.character(unique.id[i]),
                               color = "grey",
                               weight = 1
    )
    #df <- df[, .SD[c(seq(1, .N, 5), .N)]]
    device.map <- addCircleMarkers(device.map,
                                   lng = df$long_x, lat = df$lat_y,
                                   group = as.character(unique.id[i]),
                                   radius = 2,
                                   stroke = FALSE,
                                   fillOpacity = .3,
                                   color = pal[i],
                                   popup = paste(sep = "<br>",
                                                 paste("<b>NDOW ID:</b> ", unique.id[i]),
                                                 paste("<b>timestamp:</b> ", df$timestamp),
                                                 paste("<b>LocID</b>: ", df$locid))
    )
    layer.group <- c(layer.group, as.character(unique.id[i]))
  }
  device.map <- addLayersControl(device.map, overlayGroups = layer.group)
  return(device.map)
}
{% endhighlight %}

The output of this function looks like this.

![DeviceMapping output](/assets/devicemap.jpg)
<div class="caption">
  <p class = "caption-text">
    <em>Example data from MoveBank plotted using the DeviceMapping function.</em>
  </p>
</div>

The last function is used to add polygon layers to the DeviceMap from above. The web application estimates several homeranges for each animal. The home range polygons are saved as a GeoJSON and displayed on the map with the `addGeoJSON` function of leaflet. Like the above functions, it loops through all the unique animals in the GeoJSON and maps them to the correct color. Creating the GeoJSON is a whole other process I'll go over some other time.

{% highlight r %}
DeviceMapping_geojson <- function(device.map, geojson) {
  pal <- ggthemes::gdocs_pal()(20)
  for (i in seq_along(geojson)) {
    device.map <- addGeoJSON(device.map, geojson[[i]], color = pal[i],
                             weight = 1, group = names(geojson)[i])
  }
  return(device.map)
}
{% endhighlight %}

The result of the DeviceMapping functions produce the following map. The `DeviceMapping` function plots the points and the `DeviceMapping_geojson` functions maps the polygons.

![DeviceMapping output](/assets/mapud.jpg)
<div class="caption">
  <p class = "caption-text">
    <em>Estimated kernel density utilization distribution for 3 animals. The contours are 50, 75, and 95 percentiles.</em>
  </p>
</div>

All these functions are used in the web application. They've also proven useful for everyday use in the office. To generalize or modify them for your specific use case, the major things that need to be changed are the name of the ID field. Our animals ID is called ndowid. Other places are the values used for the labels in the popups when the circle markers are clicked.
