---
layout: post
title: "Scraping stackoverflow careers for fun and profit"
date: 2016-07-12 07:55:13 +0200
comments: true
categories:
 - scrapy 
 - stackoverflow  
 - python 
---
When you want to learn something new the best way to do is to come up with a problem that can be useful to you or maybe to others and then solve it. Not so long ago I decided that I want to start learning something data related. So I came up with the idea that I would create a solution that gathers the data from [Stackoverflow Jobs](http://stackoverflow.com/jobs). Do some manipulation on it  and then present it in a nice format. This is the first step of the journey. 

To present something, I need data. To get the data, I need to write a script that gathers data from the web. I decided to use python and [Scrapy framework](http://scrapy.org). 
<!--more--> 

### Gearing up… 
First, some disclaimers. I am new to python and to scrapy as well. I had zero knowledge any of these topics before. I am learning this stuff as I write these posts. Any constructive and helpful comments are more than welcome. 

The code for this project can be found in [stackjobs repo on github](https://github.com/gyurisc/stackjobs).
 
After installing Python and [Scrapy](http://scrapy.org), the first thing is to create the web-scraper project by typing the following  command(s) in your console:

``` bash
		scrapy startproject stackjobs
		cd stackjobs
		scrapy genspider Stackjob stackoverflow.com
```

With these commands the following has been achieved. First we created a new project called stackjobs. The script created a folder structure with files in it. Then we entered the folder and created a spider called Stackjob using the genspider command. This created a spider that can crawl stackoverflow.com. 

Looking at the spiders folder, we can see the source file for Stackjob spider: 

``` python spider created 
		# -*- coding: utf-8 -*-
		import scrapy


		class StackjobSpider(scrapy.Spider):
    		name = "Stackjob"
    		allowed_domains = ["stackoverflow.com"]
    		start_urls = (
      	  'http://www.stackoverflow.com/',
    		)

    		def parse(self, response):
      	  		pass
``` 

You can run the spider this by executing the following command: 

``` bash running the spider 
scrapy crawl Stackjob
```

This outputs a bunch of stuff that we are not interested in really.  

``` bash bunch of stuff we are not really interested in
2016-07-11 22:22:30 [scrapy] INFO: Scrapy 1.1.0 started (bot: stackjobs)
2016-07-11 22:22:30 [scrapy] INFO: Overridden settings: {'NEWSPIDER_MODULE': 'stackjobs.spiders', 'SPIDER_MODULES': ['stackjobs.spiders'], 'ROBOTSTXT_OBEY': True, 'BOT_NAME': 'stackjobs'}
…
2016-07-11 22:22:30 [scrapy] INFO: Enabled item pipelines:
[]
2016-07-11 22:22:30 [scrapy] INFO: Spider opened
2016-07-11 22:22:30 [scrapy] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2016-07-11 22:22:30 [scrapy] DEBUG: Telnet console listening on 127.0.0.1:6023
2016-07-11 22:22:30 [scrapy] DEBUG: Redirecting (301) to <GET http://stackoverflow.com/robots.txt> from <GET http://www.stackoverflow.com/robots.txt>
2016-07-11 22:22:30 [scrapy] DEBUG: Crawled (200) <GET http://stackoverflow.com/robots.txt> (referer: None)
2016-07-11 22:22:31 [scrapy] DEBUG: Redirecting (301) to <GET http://stackoverflow.com/> from <GET http://www.stackoverflow.com/>
2016-07-11 22:22:31 [scrapy] DEBUG: Crawled (200) <GET http://stackoverflow.com/robots.txt> (referer: None)
2016-07-11 22:22:31 [scrapy] DEBUG: Crawled (200) <GET http://stackoverflow.com/> (referer: None)
2016-07-11 22:22:31 [scrapy] INFO: Closing spider (finished)
2016-07-11 22:22:31 [scrapy] INFO: Spider closed (finished)
```

This basically tells us that our spider ran and downloaded zero pages exactly. We are interested in the Stackoverflow job listing, so let’s modify the Stackjob spider by adding start urls that are relevant for us: 

``` python adding start urls 
class StackjobSpider(scrapy.Spider):
    name = "Stackjob"
    allowed_domains = ["stackoverflow.com"]
    start_urls = (
        'https://stackoverflow.com/jobs?sort=p',
        'https://stackoverflow.com/jobs?sort=p&pg=2',
        'https://stackoverflow.com/jobs?sort=p&pg=3',
        'https://stackoverflow.com/jobs?sort=p&pg=4'
    )

    def parse(self, response):
        pass
```

When running the spider again we can find that still nothing really happens. The pages are downloaded, but we do not process the information on the pages. In order to process the information, let’s modify the parse method in our spider.

First we need to add a few imports: 

``` python
import scrapy
import datetime

class StackjobSpider(scrapy.Spider):
    name = "Stackjob"
    allowed_domains = ["stackoverflow.com"]
    start_urls = (
        'https://stackoverflow.com/jobs?sort=p',
        'https://stackoverflow.com/jobs?sort=p&pg=2',
        'https://stackoverflow.com/jobs?sort=p&pg=3'
    )
```

Then add some code to the _parse_ method. This code will loop through all the jobs on the page found by our path expression and will create a _StackjobItem_ for each job found. Then it will add today’s date to the newly created item. 

``` python adding parsing logic 
    def parse(self, response):
        jobs = scrapy.Selector(response).xpath('//div[contains(@class, "-item") and contains(@class, "-job")]')
        results = [] 

        t = datetime.date.today()
        
        for job in jobs: 
            item = StackjobItem()  
						item['date'] = t.strftime('%Y-%m-%d')   	

						# actual parsing will come here 

            results.append(item)

        return results 
```

When running this code, you will receive a couple of messages complaining about StackjobItem not being defined. This is expected as we still need to define this class. Open up _items.py_ and rename the class _StackjobsItem_ class to _StackjobItem_. This is the class what we will use to store the extracted data for each job. For this you will need to define a few fields. The final class will look like this: 

``` python StackjobItem with fields

import scrapy
from scrapy.item import Item, Field

class StackjobItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    title = Field()
    url = Field()
    jobid = Field()
    employer = Field()
    description = Field()    
    location = Field()
    equity = Field()
    salary = Field()
    tags = Field()    
    date = Field()
    pass
```

Now, head back to _Stackjob.py_ and import the item class. 

``` python 
from stackjobs.items import StackjobItem
```

When running the spider, you should see something like this in the output: 

``` bash 
2016-07-12 07:07:17 [scrapy] DEBUG: Scraped from <200 https://stackoverflow.com/jobs?sort=p&pg=3>
{'date': '2016-07-12'}
2016-07-12 07:07:17 [scrapy] DEBUG: Scraped from <200 https://stackoverflow.com/jobs?sort=p&pg=3>
{'date': '2016-07-12'}
2016-07-12 07:07:17 [scrapy] DEBUG: Scraped from <200 https://stackoverflow.com/jobs?sort=p&pg=3>
{'date': '2016-07-12'}
2016-07-12 07:07:17 [scrapy] DEBUG: Scraped from <200 https://stackoverflow.com/jobs?sort=p&pg=3>
{'date': '2016-07-12'}
2016-07-12 07:07:17 [scrapy] DEBUG: Scraped from <200 https://stackoverflow.com/jobs?sort=p&pg=3>
{'date': '2016-07-12'}
```

Now, this tells us that the items are being generated and each item has one field only and that is the _date_ field. This is not really useful, so let’s add more information.

### To the actual scraping 

For getting information out of the items found on the page, we will be using xpath expressions. Earlier, we added one to our parse method. This selector is responsible to find all the jobs on our page. It basically tells scrapy to return all elements that are a div and has both a *-job* and *-item* css class.    

``` python 
jobs = Selector(response).xpath('//div[contains(@class, "-item") and contains(@class, "-job")]')
```

Next, we will enumerate through the collection returned in the _jobs_ variable and extract some more information from each item and store it. 

In the _parse_ method of our spider, just below the date field add the following lines: 

``` python xpaths and css selectors
item['tags'] = job.xpath('div[contains(@class,"tags")]/p/a/text()').extract()
item['description'] = job.xpath('p[contains(@class,"text") and contains(@class, "description")]/text()').extract()[0]
item['location'] = job.xpath('ul[contains(@class, "metadata") and contains(@class, "primary")]/li[contains(@class, "location")]/text()').extract()[0].strip()
item['employer'] = job.xpath('ul[contains(@class, "metadata") and contains(@class, "primary")]/li[contains(@class, "employer")]/text()').extract()[0].strip()
item['jobid'] = job.xpath('@data-jobid').extract()[0]
item['title'] = job.xpath('div[contains(@class,"-title")]/h1/a/text()').extract()[0]
item['url'] = job.xpath('div[contains(@class,"-title")]/h1/a/@href').extract()[0]
``` 

Here, we have an xpath expression for each field that we are interested in. For coming up with the xpath expression I was using Google Chrome and its wonderful [DevTools](https://www.codeschool.com/courses/discover-devtools). You can bring this up by opening a web-page and right-clicking on any element and select _Inspect_ from the menu. 

The other stuff we need to come up with the expressions is the knowledge of css and lot’s of trial and error.  

### XPath and CSS munging 

The other stuff we need to come up with the expressions is the knowledge of css and lot’s of trial and error. Let’s see a few expressions: 

``` python title and url 
item['title'] = job.xpath('div[contains(@class,"-title")]/h1/a/text()').extract()[0]
item['url'] = job.xpath('div[contains(@class,"-title")]/h1/a/@href').extract()[0]
```

Here, we are searching with a div that has the _title_ as a css class and then we need the link _a_ under the _h1_ heading. The text from this link will be store in the _title_ field and the actual link in the _url_ field. 

``` python extracting tags
item['tags'] = job.xpath('div[contains(@class,"tags")]/p/a/text()').extract()

``` 

Here, we are looking for a div with a class _tags_ and all the text from the links underneath the paragraph.

``` python extracting the employer
 item['employer'] = job.xpath('ul[contains(@class, "metadata") and contains(@class, "primary")]/li[contains(@class, "employer")]/text()').extract()[0].strip()

``` 

Here, we are looking for an unordered list _ul_ with both a class _metadata_ and _primary_ and underneath we are looking for a list item with _employer_ class. We then extract the first item from the returned list and call _strip()_ to get rid of the whitespace characters. 

There is one last field that needs to be extracted called _salary_. This is a bit tricky, because it is not always present. 

First, we need to check, if we have an html span tag with class _salary_ our title, if we have then we just extract it and then strip out of the white spaces from the result. 

``` python checking if salary is present and extracting it 
if job.xpath('div[contains(@class,"-title”)]/span[contains(@class,"salary")]/text()').extract():
    item['salary'] = job.xpath('div[contains(@class,"-title")]/span[contains(@class,"salary")]/text()').extract()[0].strip()

``` 

Here is how the full parse method should look like: 

``` python final parse method 

    def parse(self, response):
        jobs = Selector(response).xpath('//div[contains(@class, "-item") and contains(@class, "-job")]')
        results = [] 
        
        t = datetime.date.today()
        
        for job in jobs: 
            item = StackjobItem()    
            item['date'] = t.strftime('%Y-%m-%d')                       
            item['tags'] = job.xpath('div[contains(@class,"tags")]/p/a/text()').extract()
            item['description'] = job.xpath('p[contains(@class,"text") and contains(@class, "description")]/text()').extract()[0]
            item['location'] = job.xpath('ul[contains(@class, "metadata") and contains(@class, "primary")]/li[contains(@class, "location")]/text()').extract()[0].strip()
            item['employer'] = job.xpath('ul[contains(@class, "metadata") and contains(@class, "primary")]/li[contains(@class, "employer")]/text()').extract()[0].strip()
            item['jobid'] = job.xpath('@data-jobid').extract()[0]
            item['title'] = job.xpath('div[contains(@class,"-title")]/h1/a/text()').extract()[0]
            item['url'] = job.xpath('div[contains(@class,"-title")]/h1/a/@href').extract()[0]
                        
            if job.xpath('div[contains(@class,"-title")]/span[contains(@class,"salary")]/text()').extract():
                item['salary'] = job.xpath('div[contains(@class,"-title")]/span[contains(@class,"salary")]/text()').extract()[0].strip()

            results.append(item)
            # yield item

        return results 
```

After running the parser, we should see the extra fields being added to our item. 

``` bash output from the parser 
{'date': '2016-07-12',
 'description': u'COMPANY DESCRIPTION\r\nSAP\u2019s vision is to help the world run better and improve people\u2019s lives.\r\nAs the\u2026',
 'employer': u'SAP SE',
 'jobid': u'106345',
 'location': u'Walldorf, Deutschland',
 'tags': [u'c++', u'python', u'linux', u'git', u'shell'],
 'title': u'C++ (Senior) Developer for SAP HANA',
 'url': u'/jobs/106345/c-plus-plus-senior-developer-for-sap-hana-sap-se'}
```
The code for this project can be found in [stackjobs repo on github](https://github.com/gyurisc/stackjobs).

This is exciting. In the next part, I will show how to store the results in a mongoldb database. 

**Update 2017-06-13**: Fixing small typos, editing and shortening console output.
