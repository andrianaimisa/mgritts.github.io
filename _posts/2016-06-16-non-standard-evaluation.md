---
layout: post
title: "Struggling with Non Standard Evaluation"
tags:
  - R
  - dplyr
  - data manipulation
---

All the functions in `dplyr` (and many of the packages in the "hadleyverse") use Non Standard Evaluation (NSE). NSE is extremely handy and reduces the amount of typing. However functions that use NSE aren't always intuitive to use. <!--more-->I mostly run into this issue when using `dplyr` in a Shiny applications. Most recently I've run into this issue attempting to dynamically filter a `data.frame` on different columns. This [gist][1] has a sample of the data I'm working with.

In base R filtering a `data.frame` is fairly straight forward. However a fairly intuitive attempt to use `dplyr::filter` doesn't work, instead returning an empty `data.frame`.

{% highlight r %}
x <- dat[dat$HuntUnit == 262, ]
col <- 'HuntUnit'
val <- 262
x1 <- dat[dat[, col] == val, ]
identical(x, x1)
x2 <- filter(dat, col == val)
{% endhighlight %}

In order to evaluate this expression use the `lazyeval::interp` function with `filter_`.

{% highlight r %}
x <- filter(dat, HuntUnit == 262)
# filter criteria
criteria <- lazyeval::interp(~x == val, .values = list(x = as.name(col)))
x1 <- filter_(dat, criteria)
identical(x, x1)
{% endhighlight %}

In the Shiny application I have a drop down input and text input to filter a `data.frame`. The drop down input chooses the column to filter and the text input is the criteria for the filter. I haven't been able to get this Standard Evaluation version to work with a database backend. The [`dplyr` vignette][4] mentions NSE is important for database backends. `dplyr::*_` functions are Standard Evaluation equivalents, I'm not certain they'll work for databases.

For more information about NSE look at the [Advanced R book][2] and this quick [post by Carl Boettiger][3]

[1]: https://gist.github.com/kissmygritts/571822822e321338cd78c650675ff72a
[2]: http://adv-r.had.co.nz/Computing-on-the-language.html
[3]: http://www.carlboettiger.info/2015/02/06/fun-standardizing-non-standard-evaluation.html
[4]: https://cran.r-project.org/web/packages/dplyr/vignettes/nse.html
