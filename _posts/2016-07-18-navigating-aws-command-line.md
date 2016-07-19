---
layout: post
title: "Navigating EC2 with the Command Line"
tags:
  - lab notebook
  - aws
  - ec2
  - shiny server
---

Understanding the directory structure of an AWS EC2 instance is extremely helpful in order to get your apps running. My last post explained how to get an AWS EC2 instance up and running with Shiny Server, this post will layout the directory structure and navigating to important files.<!--more-->

*Note: I'm using an Ubuntu 14.04 Amazon Machine Image. The default user is ubuntu. I'll use 11.22.33.44 as an example IP address. Be sure to replace this with your IP address when you see it. The public DNS will use the same convention, however the `.`s will be replaced with `-`.*

## Loggin In

Log into an EC2 instance using the secure key, public DNS and `ssh`. When launching an EC2 instance you'll download an secure key pair and the public DNS is available on the EC2 dashboard. Replace everything after ubuntu@ with your public DNS.

{% highlight bash %}
$ ssh -i ~/awskey.pem ubuntu@ec2-11-22-33-44.us-west-2.compute.amazon.aws
{% endhighlight %}

If you're logged in you should see some info about your instance and the text before the `$` should say `ubuntu@ip-11-22-33-44`.

## Directory Structure and Navigation

The starting directory is the `/home/ubuntu` or `~`. This is the user directory. Each user on your system has a directory under `/home`. This is very similar to any other operating system. Here is the general layout for an EC2 instance.

{% highlight text %}
| /
| | home
| | | ubuntu/
| | srv
| | | shiny-server
| | | | index.html
| | | | apps/
| | var
| | | log/
| | etc
| | | nginx/
| | | shiny-server/
{% endhighlight %}

These are the folders that are most important for running a shiny server. `/srv/shiny-server` includes all is where all shiny app related things should go. `/var/log/` is the location for system and server logs. Any errors that occure because of the server or non-zero exit status for R will be located here. `/etc/nginx` and `/etc/nginx/shiny-server` are the location of server configuration files that may need to be changed to set up or change server behavior.

### Navigation

Navigating the server is very similar to navigating with terminal. Some of these commands may require `sudo`.

{% highlight bash %}
$ pwd           --> print working directory
$ ls            --> list files of current directory (ls -a list all files)
$ cd path       --> change directore (replace /dir with path)
$ cp curr dest  --> copy files from curr(ent) path to dest(ination) path
$ mv curr dest  --> move files from curr(ent) path to dest(ination) path
$ rm path       --> delete files in path (rm -rf to delete a directory)
$ nano path     --> edit current file in path
$ /             --> root directory
$ .             --> current directory
$ ..            --> directory one level up
$ *             --> all files ($ *.csv all csv files*)
{% endhighlight %}

So, to change the directory to root use `cd /`, to go back to the user directory, `cd ~`. To edit the shiny server config `sudo nano /etc/shiny-server/shiny-server.conf`. To move files from the user directory to shiny server, `sudo mv ~/*.csv /srv/shiny-server/apps/`.

## Moving Data into EC2

There are a few different methods for moving data into an EC2 instance. You could use and ftp client, git clone, or use `scp` (secure copy). I always use secure copy. I find it just as easy as setting up an ftp client, and git is only good if your files are small. The following command will copy all the files in the documents folder to `/home/ubuntu/`

{% highlight bash %}
$ scp -i ~/awskey.pem ~/documents/* ubuntu@ec2-11-22-33-44.us-west-2.compute.amazonaws.com:~
{% endhighlight %}

`scp` can only copy files into a directory that the current user has write permissions to. User ubuntu has permission to write to `/home/ubuntu/` but not `/srv/shiny-server/apps/data/` the location I store any data for an app. Once the data is on EC2 I move it to the appropriate folder. Then delete the data from the home folder, if it is still there.

{% highlight bash %}
$ sudo mv ~/* /srv/shiny-server/apps/data/
$ rm ~/uploaded.files
{% endhighlight %}

Thats about it for navigating an EC2 instance. I'll add to this post as I use more command line tools with EC2.
