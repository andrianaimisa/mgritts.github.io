---
layout: post
title: "Weekly Project 2: MongoDB, Express and Node Blog App"
comments: true
tags:
  - weekly code project
  - html
  - css
  - javascript
  - node
  - express
  - mongodb
---

Something a little more ambitious this week, still pretty easy though. A simple blog app with MongoDB, Express, and Node. This was fairly similar to the first few steps of the [microblog app][1] for the Flask framework. I kept it even simpler. My primary goal was to familiarize myself with the Express framework and RESTful routes.<!--more-->

If your unfamiliar with the technologies used, [MongoDB][2] is used for the database to store blog posts. [Node][4] is a JavaScript runtime for server-side programming. [Express][3] is a web framework for Node. It is relatively simple and minimalist.

## About the app

A blog app is a fairly standard tutorial for many frameworks. In the past I've tried to follow the microblog tutorial for flask. I didn't get very far, and never completed it. For this app I followed instructions from the Udemy course [The Web Developer Bootcamp][5]. I highly recommend the course, I've been working through it for a month and have found it very straight forward, and the instructor is great. It is really good for beginners with no experience in web development.

For the user interface I used the Semantic UI framework. In the future I may do some more work on this project. I can flush out the homepage a little more, and work on the blog entry portion a little. I would like to set up a similar application with a Postgres database in the future.

## Nuggets & Pearls

A few of the biggest takeaways from this weeks project.

1. `mongod --dbpath ~/data/db` to start the MongoDB client, be sure Mongo is running before trying to run the app
2. [RESTful][6] routes are the standard
3. I don't really like Semantic UI's documentation, it isn't as easy as Bootstrap
4. `node app.js` to serve your app
5. Callbacks are a completely foreign concept to me, I must look into it more.
6. HTTP only supports GET and POST request, method override resolves this

As always the code can be found on [GitHub][7]


[1]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
[2]: https://docs.mongodb.com/?_ga=1.18191999.719737569.1469129900
[3]: https://expressjs.com/
[4]: https://nodejs.org/
[5]: https://udemy.com/
[6]: http://restfulrouting.com/#golden-seven
[7]: https://github.com/kissmygritts/coding-challenge/tree/master/week2
