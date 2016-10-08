---
layout: post
title: "Weekly Project Update"
comments: true
tags:
  - weekly code project
---

It has been a while, and not for lack of trying. I've figured out the the ins and outs of Sequelize-CLI, I've been working on building multiple associations and how those work. I've been trying to figure out some jQuery and Node nuances. Unfortunately I've been swamped with a deadline at work and spending all my time getting the forms/apps for work together, so I haven't had much time to explore beyond my work.<!--more-->

Anyway, I recently read an article by [John Resig about coding every day][1]. Every day he attempts to code at least one hour on side projects. This post was written in 2014 and if you check his GitHub account, you'll see he has a commit every day this year! I've been trying my hardest to follow this advice, unfortunately the work hasn't been on side projects.

Next, a bit of code that I recently figured after days of struggling.

{% highlight javascript %}
function parseDynamicContent(rq) {
    var n = 0;
    var dat = [];
    while (n < rq[Object.keys(rq)[0]].length) {
        var m = 0;
        var obj = {};
        while (m < Object.keys(rq).length) {
            var val = rq[Object.keys(rq)[m]][n];
            if (val == '') {
                val = null
            }
            obj[Object.keys(rq)[m]] = val
            m++;
        }
        dat.push(obj)
        n++;
    }
    return dat;
}
{% endhighlight %}

I need this little function to parse dynamically created form elements to input into a database. Here is an example of the raw form content (ExpressJS `request.body`).

{% highlight javascript %}
request.body.marks = {
		"mark_type": [
			"Ear Tag",
			"Ear Tag",
			"Pit Tag"
		],
		"mark_id": [
			"123",
			"567",
			"12348981723"
		],
		"mark_color": [
			"Green",
			"Yelloq",
			"NA"
		],
		"mark_location": [
			"Right Ear",
			"Left Ear",
			"Shoulders"
		],
		"date_given": [
			"2016-10-07",
			"2016-10-07",
			"2016-10-07"
		],
		"mark_removed": [
			"2016-10-28",
			"2016-10-28",
			""
		]
}
{% endhighlight %}

The function iterates over each array and creates a new array of objects to conform to SequelizeJS's create API.

{% highlight javascript %}
marks = [
	{
		"mark_type": "Ear Tag",
		"mark_id": "123",
		"mark_color": "Green",
		"mark_location": "Right Ear",
		"date_given": "2016-10-07",
		"mark_removed": "2016-10-28"
	},
	{
		"mark_type": "Ear Tag",
		"mark_id": "567",
		"mark_color": "Yelloq",
		"mark_location": "Left Ear",
		"date_given": "2016-10-07",
		"mark_removed": "2016-10-28"
	},
	{
		"mark_type": "Pit Tag",
		"mark_id": "12348981723",
		"mark_color": "NA",
		"mark_location": "Shoulders",
		"date_given": "2016-10-07",
		"mark_removed": null
	}
]
{% endhighlight %}

I spent hours searching the internet trying to figure this out and couldn't find much help. Hopefully this may help some folks, if you have any recommendations on how to improve this code, comments are much appreciated.

Over the last several weeks of reading many JavaScript new aggregators it has become clear that I should learn React. The next post (due October 16th!) should include some React if everything goes well. 

[1]: http://ejohn.org/blog/write-code-every-day/
