---
layout: post
title:  "rNDOW package"
tags:
  - R
  - package
---

Over the last few months I've been writing tons of R functions. They all go to GitHub, but they aren't easily used in R. Each time I want to use one I have to use `source('url.to.repo')`. I finally decided it was time to write an R package to more easily access these functions, and make them more user friendly for my colleagues.
<!--more-->

## Writing R packages

The best sources to help write R packages are Hilary Parker's [quick post](http://hilaryparker.com/2014/04/29/writing-an-r-package-from-scratch/) about writing a personal R package, and Hadley Wickham's [R Packages book](http://r-pkgs.had.co.nz/). I won't go into detail about how to write a package as these are two great source. I will say that the easiest way to write a package is to use RStudio and distribute on GitHub.

## Push to GitHub

Once your `.R` files are saved and `.Rd` files are generated with `devtools::document()` push to a GitHub repo. Now all you have to do in order to install you package is `devtools::install_github('username/repo')`. Now your package is installed and usable by you and your colleagues.

## rNDOW

The `rNDOW` package is very early in development. I've added three functions that I use very frequently for exploratory analysis and visualization of animal movement data (post on that soon!). The package will be used by my colleagues to interface with our data management systems. Due to this, many functions will have hard-coded or default values that make data analysis easier for us. Most of the functions will encapsulate common data munging, exploration, and visualization procedures.

### Functions

There are 4 functions in the package, `xyConv`, `moveParams`, `plotTraj` and `plot3DTraj`. I've included a randomly sampled animal trajectory for examples `muldDat`. The packages can be found at the [NDOW-ARG/rNDOW repository](https://github.com/NDOW-ARG/rNDOW). The functions solve a common workflow I have in R, converting latlong coordinates to UTM, adding parameters for animal movement models, and plotting the animal trajectory for exploratory visualization. More information on the input and specifics of the functions can be found at the bottom of the post.

### Use

Here is an example of how I use the functions.

{% highlight r %}
data(muldDat)
df <- xyConv(muldDat)
df <- moveParams(df$X, df$Y, df$timestamp, dat = df, isPOSIXct = F)
plotTraj(df$X, df$Y)
plot3DTraj(df$X, df$Y, df$timestamp, df$timestamp)
{% endhighlight %}

Line 1 calls the data from the package. Line 2 converts the coordinates to UTM Zone 11, the default options work for me. Line 3 adds the movement parameters to the dataframe. The timestamp isn't class `POSIXct` so I convert it using the `fasttime` library. Line 4 and 5 plot the trajectory. Line 5 creates a "space time cube", a visualization of the spatial and temporal distribution of the animals GPS locations.

## About the functions

### `xyConv`

Convert geographic coordinates from one coordinate system to another. The input is:

1. `df` - a `data.table` or `data.frame` with the geographic coordinates.
2. `xy` - the names of the x and y coordinates.
3. `CRSin` - the `proj4string` of the input coordinates.
4. `CRSout` - the `proj4string` for the coordinates to be converted to.
5. `outclass` - the class for the output object. There are three options
  1. `data.frame` - output as a `data.frame` with converted coordinates.
  2. `data.table` - output as a `data.table` with the converted coordinates.
  3. `spdf` - output as a `SpatialPointsDataFrame` with the converted coordinates as the `@coord` slot.

Each output class has its pros and cons. I like working with the `data.table` package. Converting to `SpatialPointsDataFrame` makes writing shapefiles and plotting easier.

{% highlight r %}
xyConv <- function(df, xy = c('long_x', 'lat_y'), CRSin = '+proj=longlat', CRSout = '+proj=utm +zone=11',
                    outclass = 'data.table') {
  if (class(df)[1] == 'data.frame') {
    df <- as.data.table(df)
  }

  df <- na.omit(df, cols = xy)
  conv <- SpatialPoints(cbind('X' = as.numeric(df[[xy[1]]]),
                              'Y' = as.numeric(df[[xy[2]]])),
                        proj4string = CRS(CRSin))
  conv <- spTransform(conv, CRS(CRSout))
  df <- cbind(df, as.data.frame(conv))

  if (outclass == 'data.frame') {
    df <- as.data.frame(df)
  } else if (outclass == 'spdf') {
    df <- SpatialPointsDataFrame(conv, df, proj4string = conv@proj4string@projargs)
  }
  return(df)
}
{% endhighlight %}

### `moveParams`

Estimate a number of different movement parameters based on timestamped XY data. These parameters are used in many different models of animal movement. For meaningful estimates it is best to convert to a UTM or other metric projection. The input is:

1. `x` - the x coordinate.
2. `y` - the y coordinate.
3. `timestamped` - the timestamp associated with the XY coordinates.
4. `dat` - specify the `data.frame` or `data.table` to add the movement parameters to, this is optional.
5. `isPOSIXct` - whether or not the timestamp is of class `POSIXct`, if not it'll be converted. For best results the text format should be a character string as "YYYY-MM-DD HH:MM:SS".

{% highlight r %}
moveParams <- function(x, y, timestamp, dat = NULL, isPOSIXct = TRUE) {
  if (isPOSIXct == FALSE) {
    timestamp <- fasttime::fastPOSIXct(timestamp)
  }
  dist <- c(0, sqrt((x[-1] - x[-length(x)])**2 +
                      (y[-1] - y[-length(y)])**2))
  nsd <- (x - x[1])**2 + (y - y[1])**2
  dt <- c(0, unclass(timestamp[-1]) - unclass(timestamp[-length(timestamp)]))
  speed <- (dist / 1000) / (dt / 3600)
  speed[1] <- 0

  z <- x + (0 +1i) * y
  phi <- c(0, Arg(diff(z)))
  theta <- c(0, diff(phi))

  vp <- speed * cos(theta)
  vt <- speed * sin(theta)

  params <- data.frame(cbind(dist, nsd, dt, speed, phi, theta, vp, vt))

  if (is.null(dat) == TRUE) {
    return(params)
  } else {
    dat$timestamp <- timestamp
    return(cbind(dat, params))
  }
}
{% endhighlight %}

### `plotTraj`

A simple function to plot an animal trajectory. Takes X and Y coordinates as input and uses base graphics to plot the trajectory. The green and red circle indicate the beginning and end of the trajectory.

{% highlight r %}
plotTraj <- function(x, y) {
  plot(x, y, asp = 1, type = 'o', pch = 19, cex = .5, col = rgb(0, 0, 0, .2))
  points(x[1], y[1], col = 'green', pch = 19, cex = 1.25)
  points(x[length(x)], y[length(y)], col = 'red', pch = 19, cex = 1.25)
}
{% endhighlight %}

### `plot3DTraj`

Another simple function to plot the animal trajectory in 3D. Specify a Z value and value for the color. When plotted the figure will spin, and is interactive.

{% highlight r %}
plot3DTraj <- function(x, y, z, colval) {
  myColorRamp <- function(colors, values) {
    v <- (values - min(values)) / diff(range(values))
    x <- colorRamp(colors)(v)
    return(rgb(x[, 1], x[, 2], x[, 3], maxColorValue = 255))
  }
  if ('POSIXct' %in% class(colval)) {
    colval <- 1:length(colval)
  }
  xyPanel <- min(z, na.rm = T) - 10

  cols <- myColorRamp(c('purple', 'springgreen', 'yellow'), colval)

  plot3d(x, y, z, type = 'l', col = 'darkgrey')
  plot3d(x, y, z, type = 'p', col = cols, add = T, size = 5)
  plot3d(x, y, xyPanel, type = 'l', col = 'lightgrey', add = T)


  play3d(spin3d(rpm = 3), duration = 20)
}
{% endhighlight %}

### `muldDat`

A randomly sampled animal trajectory. There are 500 rows and 3 fields:

1. long_x - longitude coordinate
2. lat_y - latitude coordinate
3. timestamp - character of the date/time the coordinates were taken (YYYY-MM-DD HH:MM:SS)

## Future work

The immediate goal is to provide a framework for more complex movement analysis, including the estimation of home ranges and segmenting trajectories based on behavior.
