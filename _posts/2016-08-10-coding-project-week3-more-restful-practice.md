---
layout: post
title: "Weekly Project 3: More RESTful Practice"
comments: true
tags:
  - weekly code project
  - html
  - css
  - javascript
  - node
  - express
  - mongodb
  - datatables
---

This week I decided to engrain REST into my mind by doing another project where that was the main focus. I designed a website for data entry of wildlife observations. It is pretty basic, and follows the guidelines I learned in the online web developer bootcamp I purchased. Again, nothing too fancy, just more practice.<!--more-->

The one cool additional feature is that I used the [datatables jQuery plugin][1] to build the index page. I did this because the data is mostly numbers, there aren't any cool photos or post captions to include. The idea is still the same, display all the records in the database in a nice layout and have links to show, update, delete routes.

<div class="photo-caption">
  <img src="/assets/week3.jpg" alt="week 3" />
  <p class = "caption-text">
    <em>The /encounters route for this weeks project. I used the datatables jQuery plugin to make the table of summary data.</em>
  </p>
</div>

This weeks challenge actually serves 2 purposes, my weekly challenge project and it'll be useful at work. I've been working on it for about 2 weeks. Even though it is only Wednesday I consider it this weeks project. Perhaps this is week 3a, I may add a little to it by the end of the week. I have some ideas to add a leaflet map to the index page to show a map of the encounter locations. Maybe incorporate some of the leaflet functionality I worked on in week one to improve the location input on the data entry form.

## Nuggets & Pearls

### Datatables
Datatables is a very comprehensive library for displaying spreadsheet like data on a web page. There are some very cool features, and setting it up is pretty easy. I pretty much copy, then pasted the basic setup to get my table working. I suppose the one portion that took a little experimenting was adding the buttons to view, edit, and delete the post.

### MongoDB

I do all of my development on a Mac (old 2011 MacBook Pro). About a year ago I discovered [homebrew][2] and have been using it ever since as a package manager for Macs. I installed MongoDB with homebrew to keep it simple. After installation Mongo needs a location to store the local data, then start it up.

{% highlight bash %}
# install mongo
brew install mongodb
# create directory for local data
mkdir -P ~/data/db
# initialize mongo
mongod --dbpath ~/data/db
# open new terminal window/tab to check
mongo
show dbs
{% endhighlight %}

### RESTful routes

I found this very [handy table][3] to help me remember how to set up the routes.

## Wrap Up

Check out my [introduction to this challenge][4] for more information, a table of contents, and my motivations. The code, as always is on [GitHub][5]. Comments, questions, and input are always welcome. 

[1]: https://datatables.net/
[2]: http://brew.sh/
[3]: https://www.google.com/search?q=restful+routes+table&biw=1212&bih=951&tbm=isch&imgil=EaQ62q403MZYjM%253A%253BIU2FNAJt9t9RyM%253Bhttp%25253A%25252F%25252Fdenineguy.github.io%25252Fblog%25252F2014%25252F09%25252F22%25252Fgetting-started-with-rails&source=iu&pf=m&fir=EaQ62q403MZYjM%253A%252CIU2FNAJt9t9RyM%252C_&usg=__Rb-Bqy-mt2ILxoB1XljuEaN1hwo%3D&ved=0ahUKEwiirYrPtLjOAhXBKiYKHe5ABm4QyjcILw&ei=rO-rV6KnIcHVmAHugZnwBg#imgrc=anoN7XuKaYl0lM%3A
[4]: {% post_url 2016-07-28-weekly-code-project-prologue %}
[5]: https://github.com/kissmygritts/coding-challenge/tree/master/week3
