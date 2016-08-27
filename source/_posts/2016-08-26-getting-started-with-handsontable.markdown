---
layout: post
title: "Getting Started with Handsontable"
date: 2016-08-26 13:01:03 +0200
comments: true
categories: 
- javascript 
- code
---
I am looking for a way to be able to display and manipulate tabular data on a webpage. For this, I decided to evaluate a few javascript based libraries and see how they can be used. 

The first such library is called [handsontable](https://handsontable.com/). Handsontable is a Javascript/HTML5 Spreadsheet library for developers. It seems that Handsontable is more for building Excel like user interfaces than just simple data grid control. 

So let’s look how easy to build a minimal example using with [handsontable](https://handsontable.com/)… 

{%img /images/handsontable_minimal.png ‘Handsontable Demo’ ‘Handsontable demo page' %}

<!--more--> 

The main web-page describes this library as: 

{% blockquote %}
Handsontable is a composite spreadsheet component for apps and websites. It is written in JavaScript and not constrained by any external framework. Handsontable can be easily modified or extended with custom plugins. It also binds to any source using the JSON format and is capable of handling a large amount of data. You can easily do all CRUD operations and provide end-users with an Excel-like experience.
{% endblockquote %}

### Creating the minimal Handsontable page

First of all, click to [download the source code](/downloads/handsontable_sample.zip) for the minimal handsontable sample. 

The first file to be created is the index.html 

``` html handsontable index.html
	<html>
    <head>
        <meta charset="utf-8"/>
        <meta name="apple-mobile-web-app-capable" content="yes" />
        <meta name="apple-touch-fullscreen" content="yes" />
        <title>Handsontable Minimum Sample</title>
        <link rel="stylesheet" type="text/css" href="assets/css/handsontable.full.css">
    </head>
    <body>
        <div id="table"></div>
        <script type="text/javascript" src="assets/handsontable.full.js"></script>
				<!-- Placeholder to Initialize Handsontable -->
    </body>    
</html> 
```

Nothing too complicated here. There is a only standard html page. The table will be rendered in the first called table __div__ after the body. 

``` html handsontable div
	 <div id="table"></div>
```

In the header part, the css for handsontable is referenced and in the bottom part of the body we referenced the script file needed for our table. I choose to include these files in the assets folder in the zip file, but it is possible to reference these files directly from handsontable.com as well by replacing the links like this: 

For CSS use: 

``` html handsontable css reference 
	<link rel="stylesheet" type="text/css" href="http://docs.handsontable.com/pro/bower_components/handsontable-pro/dist/handsontable.full.min.css">
```

For the script: 

``` html handsontable javascript reference 
	<script src="http://docs.handsontable.com/pro/bower_components/handsontable-pro/dist/handsontable.full.min.js"></script>
```

The important part comes after in a separate script tag. We are going to get the data for the table and initialise how the table should look like. 

``` javascript initializing our table 
   <script type="text/javascript">
        var data = [
                    {company: "Apple Inc.", ticker: "AAPL", price: "108.03", low52: "89.47", high52: "123.82", pe: "12.62", yield: "0.57"},
                    {company: "Amazon.com, Inc.", ticker: "AMZN", price: "757.25", low52: "466.25", high52: "773.75", pe: "188.83", yield: "-"},
                    {company: "Tesla Motors Inc", ticker: "TSLA", price: "222.62", low52: "141.05", high52: "271.57", pe: "-", yield: "-"},
                    {company: "Microsoft Corporation", ticker: "MSFT", price: "57.95", low52: "40.39", high52: "58.50", pe: "27.57", yield: "0.36"},
                    ]; 

            var table = document.getElementById('table');
            var hTable = new Handsontable(table, {
                data: data,              
                rowHeaders: true, 
                colHeaders: ['Company', 'Ticker', 'Price', '52 Low', '52 High', 'P/E', 'Dividend Yield'], 
                colWidths: 120, 
                rowHeights: 23, 
                autoRowSize: false, 
                autoColSize: true,  
            });
        </script>
```  

First, we create a structure called data and fill it with our stock data that we would like to display in our table.

Then, we get the placeholder div and call  the init function of the Handsontable library. In the init function, we pass the placeholder div and some other parameters like our data and the names of our columns. 

This is really it. Could not be simpler, right? 

### Some extras

I would like to add the capability to be able to sort by column names. This is very easy, just pass two more parameters in the init function 

``` javascript Enabling column ordering 
		columnSorting: true,
		sortIndicator: true,
```

Then I just would like to be able to resize the columns of my table. Again, very easy: 

``` javascript Resizing columns
   	manualRowResize: true,
    manualColumnResize: true
```
 
### Summary 

Handsontable seems like a very powerful library to build spreadsheet functionalities to your web application. It comes in two favours. The [Pro](https://docs.handsontable.com/pro/1.5.1/tutorial-introduction.html) version will cost you money, but has really nice features, such as column filtering. The simple version is open-source and as far as I understand you can use it however you see fit. 

### Links

This article will also server as a note for my future self, so here are the relevant links for this library: 

- [Handsontable Documentation](https://docs.handsontable.com/pro/1.5.1/tutorial-introduction.html)
- [Handsontable on Github](https://github.com/handsontable/handsontable).

### Full index.html

``` html full index.html 
<html>
    <head>
        <meta charset="utf-8"/>
        <meta name="apple-mobile-web-app-capable" content="yes" />
        <meta name="apple-touch-fullscreen" content="yes" />
        <title>Handsontable Minimum Sample</title>
        <link rel="stylesheet" type="text/css" href="assets/css/handsontable.full.css">
    </head>
    <body>
        <div id="table"></div>
        <script type="text/javascript" src="assets/handsontable.full.js"></script>
        <script type="text/javascript">
        var data = [
                    {company: "Apple Inc.", ticker: "AAPL", price: "108.03", low52: "89.47", high52: "123.82", pe: "12.62", yield: "0.57"},
                    {company: "Amazon.com, Inc.", ticker: "AMZN", price: "757.25", low52: "466.25", high52: "773.75", pe: "188.83", yield: "-"},
                    {company: "Tesla Motors Inc", ticker: "TSLA", price: "222.62", low52: "141.05", high52: "271.57", pe: "-", yield: "-"},
                    {company: "Microsoft Corporation", ticker: "MSFT", price: "57.95", low52: "40.39", high52: "58.50", pe: "27.57", yield: "0.36"},
                    ]; 

        var cols = [{ data: 'company', type: 'text', width: 140}, 
                    { data: 'ticker', type: 'text'}, 
                    { data: 'price', type: 'numeric'}, 
                    { data: 'low52', type: 'numeric'},
                    { data: 'high52', type: 'numeric'},
                    { data: 'pe', type: 'numeric'},
                    { data: 'yield', type: 'numeric', format: '0.00'}];               

            var table = document.getElementById('table');
            var hTable = new Handsontable(table, {
                data: data,
                columns: cols, 
                rowHeaders: true, 
                colHeaders: ['Company', 'Ticker', 'Price', '52 Low', '52 High', 'P/E', 'Dividend Yield'], 
                colWidths: 120, 
                rowHeights: 23, 
                columnSorting: true,
                sortIndicator: true,
                autoRowSize: false, 
                autoColSize: true,  
            });
        </script>
    </body>    
</html>
```