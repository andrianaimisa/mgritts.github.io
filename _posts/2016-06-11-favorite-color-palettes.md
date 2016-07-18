---
layout: post
title: "A few of my favorite color palettes"
tags:
  - r
  - ggplot2
  - data visualization
---

Good design is important. Don't believe me, maybe you'll like this [TED radio hour][1] episode. Good design is especially important in data visualization and data communication. Colors, scales, jitter, figure type are all decisions that need to be considered. Two of my favorite palettes are `ggplot2` extensions: `ggthemes` and `viridis`.
<!--more-->

{% highlight r %}
library(ggplot2)
library(leaflet)
library(ggthemes)
library(viridis)

data("diamonds")
x <- runif(10000, 0, 53940)
dat <- diamonds[x, ]

gg <- ggplot(dat, aes(x = carat, y = price, color = color)) +
  geom_point()
gg
gg + scale_color_gdocs()
gg + scale_color_viridis(discrete = TRUE)
{% endhighlight %}

![better than ggplot default color](/assets/colorpal.jpeg)
<div class="caption">
  <p class = "caption-text">
    <em>Aren't these color palettes much nicer than the default ggplot2 palette?</em>
  </p>
</div>

Each of these packages have additional color palettes that can be used, but these two are my favorite. The gdocs palette from `ggthemes` has 20 colors, more than enough for most applications. The default `viridis` color is generally used for continuous variables, however it can be used for discrete values if used with `discrete = T` argument.

## Outside `ggplot2`

It is possible to use these colors outside the `ggplot2` world. Which is great, because the default colors for base R graphics are even more attrocious than `ggplot2`. use the following function for each package, `gdocs_pal()(n)` and `viridis(n)` where n is the number of colors needed.

{% highlight r %}
plot(dat$carat, dat$price, pch = 19, col = factor(dat$color))
plot(dat$carat, dat$price, pch = 19, col = gdocs_pal()(7))
plot(dat$carat, dat$price, pch = 19, col = viridis(7))
{% endhighlight %}

![much better than base r graphics](/assets/basercolor.jpeg)
<div class="caption">
  <p class = "caption-text">
    <em>So much better than base R graphics default colors!</em>
  </p>
</div>

You can use the same functions to grab colors anytime you need a color palette. Even in leaflet.

![leaflet with gdocs pal](/assets/leaflet_ex2.png)
<div class="caption">
  <p class = "caption-text">
    <em>The gdocs_pal used in a leaflet map.</em>
  </p>
</div>

Just a quick post about making better figures. The `ggthemes` has many more color palettes, all of which can be accessed with `x_pal()` where x is the name of the ggtheme. For whimsical color palettes be sure to check out the [`wesanderson` package][2], a set of palettes inspired by the films of Wes Anderson. Or the [`yarrr` package][3], or learn to [create your own][4].

[4]: http://blenditbayes.blogspot.com/2014/05/towards-yet-another-r-colour-palette.html
[3]: http://nathanieldphillips.com/2015/10/piratepal-an-r-color-palette-function-for-pirates-or-how-to-plot-the-mona-lisa/
[2]: https://github.com/karthik/wesanderson
[1]: http://www.npr.org/programs/ted-radio-hour/478560031/the-power-of-design?showDate=2016-05-20
