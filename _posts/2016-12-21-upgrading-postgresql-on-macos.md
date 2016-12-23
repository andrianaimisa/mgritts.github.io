---
layout: post
title: "Upgrading PostgreSQL on macOS"
comments: true
tags:
  - postgresql
  - lab notebook
---

While working on building a spatial database system I needed to update PostgreSQL from 9.5.3 to 9.6.1 in order to use the most recent version of PostGIS.<!--more--> I installed postgres with homebrew.

I first updated homebrew, then upgraded postgres.

{% highlight bash %}
$ brew update && brew upgrade postgresql
{% endhighlight %}

Then I turned off the old version (9.5.3) with homebrew. Without doing this you'll be unable to update.

{% highlight bash %}
$ brew services stop postgresql
{% endhighlight %}

Next I started the new version of postgres.

{% highlight bash %}
$ initdb /usr/local/var/postgres9.6.1 -E utf8
{% endhighlight %}

Finally, I attempted to migrate the data from 9.5.3 to 9.6.1.

{% highlight bash %}
$ pg_upgrade \
  -d /usr/local/var/postgres \
  -D /usr/local/var/postgres9.6.1 \
  -b /usr/local/Cellar/postgresql/9.5.3/bin \
  -B /usr/local/Cellar/postgresql/9.6.1/bin \
  -v
{% endhighlight %}

This didn't work. The error was due to the `postmaster.pid` file in `/usr/local/var/postgres`. If this happens rename the file and rerun the above command.

Now move the postgres files to the the original location. That way we don't have to change where postgres looks for the data.

{% highlight bash %}
$ mv /usr/local/var/postgres /usr/local/var/postgres9.5.3
$ mv /usr/local/var/postgres9.6.1 /usr/local/var/postgres
{% endhighlight %}

PostgreSQL 9.6.1 is up and running and ready to go.

## Resources
[Homebrew and PostgreSQL](https://keita.blog/2016/01/09/homebrew-and-postgresql-9-5/)
