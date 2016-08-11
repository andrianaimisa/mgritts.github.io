---
layout: post
title: "One Coding Project a Week: Prologue"
comments: true
tags:
  - weekly code project
---

I've been writing code almost every (work)day for the last 2 years. My skills have improved a ton, but mainly in R, Python and VBA/SQL. I've learned a little HTML, CSS and JavaScript along the way. But I'm no where near where I want to be.<!--more-->

**TL;DR** - I'm doing one (mostly MEAN, sometimes R) coding project a week.

## Weekly Project - Table of Contents

| Week | Project | Links |
| --- | --- | --- |
| 1 | Exploring Leaflet | [blog][7] - [code][8] |
| 2 | Mongo, Express, Node Blog App | [blog][9] - [code][10] |
| 2 | More RESTful Practice | [blog][11] - [code][12] |

## Background

### In the Beginning...

My introduction to coding was during my junior year of college. I was enrolled in a field ecology class and portions of the lab required some simple experiments and statistics. There was a finally project, we could choose our own mini-experiment, analyze the data and write a paper. During my project I stumbled across R, [a statistical computing language][1], and was determined to use this language for my final project. The decision was due to my frustration with learning new statistical packages (both open and closed source) for every class I had.

I was also working as an undergraduate research assistant in a genetics (molecular ecology) lab. I used R and several other phylogenetic software programs for that work. Again, it was frustrating having to convert file formats to move from one program to another. So I did as much work as I could in R.

### The Dream Job?

In 2013 I began working for the Nevada Department of Wildlife. My initial duties were data entry and QA/QC, then grew to include database development and management. This all happened within Microsoft Access (I can feel your eyes rolling). We had (still have) very few options! I work in a small group with three colleagues and all our backgrounds are in wildlife ecology or GIS (Geographic Information Systems). Access was a common relational database management system we could all use and work in fairly efficiently without too much time commitment or too steep of a learning curve.

After another year I began doing working in R more frequently. Mostly for one off data analyses or very simply proof of concept Shiny applications. I managed to build a few cool apps that turned out to be very useful. However these apps are limited by using Access as our RDBMS.

## Currently

I've since refined and restructured my useful Shiny apps, migrated some of our data to a Microsoft SQL Server, and deployed the apps on AWS. I set myself a very aggressive timeline to complete and deploy the apps, 8 weeks. In doing so, I discovered how well I worked with deadlines, even self imposed deadlines. In order to deploy these apps I had to learn several AWS services, Linux command line, SQL Server, and Docker (didn't end up using Docker, yet).

<div class="photo-caption">
  <img src="/assets/devtimeline.png" alt="AWS deploy timeline." />
  <p class = "caption-text">
    <em>Development and deployment timeline I set for myself to develop and deploy 2 shiny apps on AWS.</em>
  </p>
</div>

Needless to say, I was surprised that I was able to meet those deadlines (ok, I ended up being one day behind schedule). Those 8 weeks were a whirlwind. I learned a lot, but only scratched the surface. [This blog][2] post really hit home on many levels, most importantly to this endeavor, "Writing software = learning something you don't know when you start." I tend to forget this. I often tell my coworkers, "Yeah, I can do that." when I really mean, "Yeah, that can definitely be done. I can probably figure it out, but I have no idea how to do it right now."

With that in mind, have a major dilemma on my hands. I'll try to lay it out simply. I work for NDOW, a major aspect of my job is to go out with field biologists for captures. Capture season starts at the end of October. I've managed to move a decent portion of the 'data out' end of our Access database to an Shiny application. The 'data in' portion still exists in Access. So I have two options:

1. Figure out how to get Access to talk to the SQL Server database for data entry and QA/QC. This would require some major rewriting to talk to SQL Server.

2. Move the entire system to the cloud. This would require learning server-side programming, better front-end skills and still require the talking to SQL Server part.

I've been given the freedom to make this decision. I really want to go route 2, but feel guilty about the initial time commitment to learn how to do this, and I'm not sure I'll hit the October deadline. Route 1 is easy and boring, but a waste of time. I'm committed to route 2.

## Thus, 1 Coding Project a Week

So here I am, desperately in need of learning a stack with too many options and a very short timeline. I've decided to go with some version of Express, Node, Angular, React, Mongo, Postgres. Express and Node for certain. However I have little to know background in any of these technologies. Thus, 1 coding project a week. I need to start somewhere, and quickly, so I'll be spending my free time learning and writing and sharing what I learn. I'll write about them on this site and will be creating a GitHub repo.

### A Few Guidelines

1. Some version of the MEAN stack (M and A may be interchanged here and there).
2. Occasionally the project will be R or Jekyll related since I frequently work on these platforms
3. Seldom a method or function to solve a problem I may be wrestling with in R.
4. The week starts on Monday and ends on Sunday.
5. If I'm not on schedule light public shaming is fair game!

### Disclosure

I am not learning from scratch, so your mileage may vary. I am not doing this (necessarily) to improve my marketability. I love my job, my goal is to make managing our data so much easier! Okay, maybe I think this might help me get into grad school (quantitative ecology).

## Inspiration

Javier Noris - [An Outline to Learning to Code in 1 Year][3]  
Jennifer Dewalt - [I'm learning to code by building 180 websites in 180 days][4]  
Mackenzie Child - [How I finally Learned to Build Stuff With Rails][5]  
OSSU - [Open Source Society University][6]  

[1]: https://www.r-project.org/
[2]: http://blog.hut8labs.com/coding-fast-and-slow.html
[3]: https://medium.com/@javier_noris/an-outline-to-learning-to-code-in-1-year-572a1a78fa62#.rc4oxq6ae
[4]: http://blog.jenniferdewalt.com/post/56319597560/im-learning-to-code-by-building-180-websites-in
[5]: https://mackenziechild.me/how-i-finally-learned-to-build-stuff-with-rails
[6]: https://ossu.firebaseapp.com/#/about
[7]: {% post_url 2016-07-30-coding-project-week1 %}
[8]: https://github.com/kissmygritts/coding-challenge/tree/master/week1
[9]: {% post_url 2016-08-08-coding-project-week2-blog-app %}
[10]: https://github.com/kissmygritts/coding-challenge/tree/master/week2
[11]: {% post_url 2016-08-10-coding-project-week3-more-restful-practice %}
[12]: https://github.com/kissmygritts/coding-challenge/tree/master/week3
