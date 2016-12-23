---
layout: post
title: "telemetR: Introduction"
comments: true
tags:
  - telemetR
---

Animal movement data is an important tool for the management of wildlife. The prices and [sizes have decreased][2] (down to 10 grams) and the capabilities and longevity of GPS tracking devices has increased. As GPS telemetry devices become more common the amount of data collected has grown substantially. The increased volume of data has created several new challenges for managing and effectively using this data<!--more-->. Especially in a field that can be loath to adopt new technologies (side note: wildlife management in practice rather than in academia is slow at adopting new technologies). These tools include data warehousing services such as [MoveBank][3], analytical tools like [adehabitat][4] suite of packages for R, and standard GIS software like ArcMap and QGIS. These tools can be inflexible and require moving data between services, file formats, and software. None of these solutions are helpful for storing the volume of data in a easy to use, personal (individual, lab group, or agency), relational database centered system that can be plugged into tools like R and GIS with packages and some coding skills.

Last year I started developing a [Shiny web application][1] (with R) called telemetR for exploratory data analysis and visualization of animal movement data. The application works great however the data is loaded as CSV. This is extremely inefficient, especially when loading millions of records.<!--more--> I've been working on migrating the data from our in-house Microsoft Access database to a PostgreSQL database on AWS to make the application more efficient. I'm currently working on the design and functionality of the database. Over the next few weeks I'll be writing posts about how I've put the system together.

## telemetR Posts

1. *[Introduction][5]*
2. [Creating an Animal Movement Database][6]
3. [Extending the Database with PostGIS][7]
4. Connecting to the Database with R
5. Adding More SQL Functionality
6. Shiny Web Application
6. A Simple RESTful API
7. ... more

[1]: http://shiny.rstudio.com/
[2]: http://www.sirtrack.co.nz/images/pdfs/LiteTrack_Collars.pdf
[3]: https://www.movebank.org/
[4]: https://cran.r-project.org/web/packages/adehabitat/adehabitat.pdf
[5]: {% post_url 2016-12-19-telemetr-intro %}
[6]: {% post_url 2016-12-20-creating-an-animal-movement-database %}
[7]: {% post_url 2016-12-22-extending-the-database-with-postgis %}
