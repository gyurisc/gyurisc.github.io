---
layout: post
title: "getting started with gridster.js"
date: 2013-03-08 17:24
comments: true
categories: 
- javascript 
- code
---
I am looking into to learn more about web client side programming, but whenever I find an interesting project I am overwhelmed with the unknown names of different libraries and technologies mentioned, while reading the project's page. Consider [Dashing](http://shopify.github.com/dashing/) for example. They mention Sinatra, [Heroku](https://github.com/Shopify/dashing/wiki/How-to%3A-Deploy-to-Heroku), jQuery.. These are the ones that I have at least a slight idea what they are good for. But the list continues: [Sprockets](https://github.com/sstephenson/sprockets), [Gridster.js](http://gridster.net/), [Server Sent Events](http://www.html5rocks.com/en/tutorials/eventsource/basics/) and [batman bindings](http://batmanjs.org/docs/batman.html#batman-view-bindings-keypath-filters). 

This is nuts, I do not understand any of these. So I decided to divide and conquer the problem and try to focus on one unknown technology at a time. The aim is not to master the library, but to construct a minimum viable sample that helps to understand what it is good for. 

Let's start with [Gridster.js](http://gridster.net/). 

{%img /images/gridster_demo.png 'Gridster demo page' 'Gridster demo page' %}

The project page describes this library as: 

<!--more--> 

{% blockquote %}
Gridster is a jQuery plugin that allows building intuitive draggable layouts from elements spanning multiple columns. You can even dynamically add and remove elements from the grid. It is on par with sliced bread, or possibly better. MIT licensed. Suitable for children of all ages. Made by Ducksboard.
{% endblockquote %}

### Making the minimum gridster page 

It seems that the library is good for creating grid layouts, where it is possible to re-arrange, add or remove dynamically the elements of the grid. Fair enough... 

So I created an empty index.html and copied all css and javascript files to the assets folder. I added the following in the index.html: 

``` html main grid
		<section class="demo">
			<div class="gridster">
				<ul>
					<li data-row="1" data-col="1" data-sizex="1" data-sizey="1"><h1>HELLO</h1></li>
					<li data-row="2" data-col="1" data-sizex="1" data-sizey="1"><h1>WORLD</h1></li>
					<li data-row="3" data-col="1" data-sizex="1" data-sizey="1"><h1>OR ELSE<h1></li>
					<li data-row="1" data-col="2" data-sizex="2" data-sizey="1"></li>
					<li data-row="2" data-col="2" data-sizex="2" data-sizey="2"></li>
					<li data-row="1" data-col="4" data-sizex="1" data-sizey="1"></li>
					<li data-row="2" data-col="4" data-sizex="2" data-sizey="1"></li>
					<li data-row="3" data-col="4" data-sizex="1" data-sizey="1"></li>				
					<li data-row="1" data-col="5" data-sizex="1" data-sizey="1"></li>
					<li data-row="3" data-col="5" data-sizex="1" data-sizey="1"></li>
					<li data-row="1" data-col="6" data-sizex="1" data-sizey="1"></li>
					<li data-row="2" data-col="6" data-sizex="1" data-sizey="2"></li>				
				</ul>
			</div>
		</section> 
```

The key here is the div with class gridster and the unordered list inside. The grid is represented by the unordered list and each of the list items inside the list are representing one grid cell in our grid. 

``` html examining one cell in the grid
	<li data-row="1" data-col="1" data-sizex="1" data-sizey="1"><h1>HELLO</h1></li>
```

Looking at a list item in greater detail, you will see that there are attributes describing the cell's position in the table using the data-row and data-col attributes and the size of the cell using the data-sizex, data-sizey attributes. 

Next we need to call some javascript in order to initialize our grid. This is done by the following javascript block

``` javascript initializing the grid
	<script type="text/javascript">
			var gridster;

			$(function() {
				gridtster = $(".gridster > ul").gridster({
					widget_margins: [10, 10], 
					widget_base_dimensions: [140, 140],
					min_cols: 6
				}).data('gridster');
			});
		</script> 
```  

Javascript and jQuery are not in my comfort zone yet, but it seems that the code selects something that has .gridster as a class and have ul underneath and sets the widget margin, base dimensions of the grid and the minimum number of columns to 6. 
 
Below, you will see the full html page and css styles. You can also [download](/downloads/gridster_sample.zip) the sample, if you like. 

### Full index.html

``` html full index.html
<!DOCTYPE html>
<html>
	<head>
		<title>gridster test</title>
		<meta name="author" content="gyurisc">
		<link rel="stylesheet" type="text/css" href="assets/css/jquery.gridster.css">
		<link rel="stylesheet" type="text/css" href="assets/css/styles.css">
	</head>
	<body>
		<section class="demo">
			<div class="gridster">
				<ul>
					<li data-row="1" data-col="1" data-sizex="1" data-sizey="1"><h1>HELLO</h1></li>
					<li data-row="2" data-col="1" data-sizex="1" data-sizey="1"><h1>WORLD</h1></li>
					<li data-row="3" data-col="1" data-sizex="1" data-sizey="1"><h1>OR ELSE<h1></l>
					<li data-row="1" data-col="2" data-sizex="2" data-sizey="1"></li>
					<li data-row="2" data-col="2" data-sizex="2" data-sizey="2"></li>
					<li data-row="1" data-col="4" data-sizex="1" data-sizey="1"></li>
					<li data-row="2" data-col="4" data-sizex="2" data-sizey="1"></li>
					<li data-row="3" data-col="4" data-sizex="1" data-sizey="1"></li>			
					<li data-row="1" data-col="5" data-sizex="1" data-sizey="1"></li>
					<li data-row="3" data-col="5" data-sizex="1" data-sizey="1"></li>
					<li data-row="1" data-col="6" data-sizex="1" data-sizey="1"></li>
					<li data-row="2" data-col="6" data-sizex="1" data-sizey="2"></li>				
				</ul>
			</div>
		</section>
		<script type="text/javascript" src="assets/jquery.js"></script>
		<script type="text/javascript" src="assets/jquery.gridster.js" charster="utf-8"></script>
		<script type="text/javascript">
			var gridster;

			$(function() {
				gridtster = $(".gridster > ul").gridster({
					widget_margins: [10, 10], 
					widget_base_dimensions: [140, 140],
					min_cols: 6
				}).data('gridster');
			});
		</script>
	</body>
</html>
```

### Full styles.css 

``` css styles.css
body {
    background-color: #EEEEEE;
    font-family: 'Helvetica Neue', Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    font-size: x-small;
    color: #666666;
}

ul, ol {
    list-style: none;
}

h1 {
  margin-bottom: 12px;
  text-align: center;
  font-size: 30px;
  font-weight: 400; 
}

h3 {
  font-size: 25px;
  font-weight: 600;
  color: white; 
}

/* Gridster styles */
.demo {
    margin: 3em 0;
    padding: 7.5em 0 5.5em;
    background: #004756;
}

.demo:hover .gridster {
    opacity: 1;
}

.gridster {
    width: 940px;
    margin: 0 auto;

    opacity: .8;

    -webkit-transition: opacity .6s;
    -moz-transition: opacity .6s;
    -o-transition: opacity .6s;
    -ms-transition: opacity .6s;
    transition: opacity .6s;
}

.gridster .gs_w {
    background: #FFF;
    cursor: pointer;
    -webkit-box-shadow: 0 0 5px rgba(0,0,0,0.3);
    box-shadow: 0 0 5px rgba(0,0,0,0.3);
}
```