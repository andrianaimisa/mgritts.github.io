---
layout: post
title: "Project 6: Vue Introduction"
comments: true
tags:
  - code better project
  - vue.j
  - front-end
---

I've read many posts about about Vue.js over the last several weeks. The framework looks like it'll fulfill many of my needs at work. Over the next several weeks I'm going to work on figuring out the nuances of setting up fullstack web applications with Vue as my view library.<!--more-->

My go to website for learning javascript is [freeCodeCamp][1]. One of the challenges at the end of the front-end development section is to build a tribute page with HTML and Bootstrap. Here is [the example tribute page][2]. Reproducing this page is easy with basic front-end web knowledge. I decided to give Vue a try as a template engine and reproduce the page. Baby steps.

## Simple Example (step 1)

The first thing I tried to do was build a simple example that laid out my the Vue logic that I needed for the tribute page. Here is what I would like this page to do, below is the code I used to make the page work. A working [example is on CodePen.][3]

1. initialize the page with data from list1
2. when the input is changed, change data to the selected list
3. render change of data on the page

{% highlight html %}
{% raw %}
<head>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/css/bootstrap.min.css">
</head>
<body>
  <div id="app" class="container">
    <h2>Testing Data</h2>
    <select class="form-control" v-model="selList" v-on:change="updateList">
      <option value="list1">List 1</option>
      <option value="list2">List 2</option>  
    </select>
    <h3>Data from {{ selList }}</h3>
    <ul>
      <li v-for="d in list">
        <p>{{ d }}</p>
      </li>
    </ul>
  </div>
</body>

<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script>
var dat = {
  list1: [ 'red', 'green', 'orange' ],
  list2: [ 'apple', 'pineapple', 'coconut' ]
}

var nv = new Vue({
  el: '#app',
  data: {
    selList: 'list1',
    list: dat.list1
  },
  methods: {
    updateList: function() {
      this.list = dat[this.selList];
    }
  }
});
</script>
{% endraw %}
{% endhighlight %}

### Template Syntax

Vue uses handlebar like syntax to render data in the DOM. This works within HTML tags, in order to render data within HTML attributes directives like `v-bind:href`.

### Directives

Directives are used to control the value changes in the DOM. They look like this `v-on`, `v-for`, `v-model`. Directives can be used to bind values to HTML attributes with `v-bind`. These allow us to handle eventes (`v-on`), conditionally (`v-if`, `v-show`) render HTML elements or render lists (`v-for`).

## Dynamic Tribute Page

The HTML skeleton for this app is fairly basic. I haven't broken the page up into components as I'm currently only trying to get a feel for the template and rendering syntax. The logic is the same as the simple example above:

1. initialize the page with data from person 1
2. when the input is changed, change data to the selected person
3. render change of data on the page

{% highlight html %}
{% raw %}
<div class="container">
  <div class="jumbotron">
    <div class="row">
      <div id="app"> <!-- this is where we mount the Vue instance -->
        <h1 class="text-center">{{ title }}</h1>
        <h2 class="text-center"><em>{{ subtitle }}</em></h2>
        <div class="thumbnail">
          <img v-bind:src="photo">
          <div class="caption text-center">{{ caption }}</div>
        </div>
        <div class="col-xs-8 col-xs-offset-2">
          <div id="accomplishments">
            <h3>Accomplishments</h3>
            <ul>
              <li v-for="acc in accomplishments">
                <b>{{ acc.year }}</b> - {{ acc.acc }}
              </li>
            </ul>
          </div>
          <div id="quotes">
            <br>
            <div id="quote" v-for="q in quotes">
              <blockquote>
                <p>"{{ q.quote }}"</p>
                <footer><cite>{{ q.person }}</cite></footer>
              </blockquote>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
{% endraw %}
{% endhighlight %}

I've excluded the data from this example, you can see a working example of the [page on CodePen][4]

{% highlight javascript %}
{% raw %}
myData = ...;
var app = new Vue({
  el: '#app',
  data: {
    sPerson: 'darwin',
    tribute: myData.darwin
  },
  methods: {
    updateTribute: function() {
      this.tribute = myData[this.sPerson];
    }
  }
});
{% endraw %}
{% endhighlight %}

As in the first example, the logic and Vue code is essentially the same. The template and rendering is a little more complex as I am working with more data and more layout.

## Wrap Up

My biggest piece of advice is to read the guide and documentation for Vue. It took me several times reading through it before everything started to click. From there experiment with Vue on CodePen for a while to get the hang of it.

[1]: [https://www.freecodecamp.com/challenges/build-a-tribute-page]
[2]: https://codepen.io/FreeCodeCamp/full/NNvBQW/
[3]: http://codepen.io/kissmygritts/pen/YpOwwo
[4]: http://codepen.io/kissmygritts/pen/pNLOzQ
