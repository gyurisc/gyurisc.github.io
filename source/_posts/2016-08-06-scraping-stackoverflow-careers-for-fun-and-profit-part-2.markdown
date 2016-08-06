---
layout: post
title: "Scraping Stackoverflow Careers for Fun and Profit - Part 2"
date: 2016-08-06 21:20:17 +0200
comments: true
categories: 
 - scrapy 
 - stackoverflow  
 - python 
 - mongoldb
---
Let’s continue with our project. To summarise what we did in the [first part](http://littlebigtomatoes.com/2016/07/scraping-stackoverflow-careers-for-fun-and-profit/), we wrote a scraper in python using the [scrapy framework](http://scrapy.org) that was capable of getting data from the stackoverflow job pages, but nothing else than that. 

In this part, we save the results to mongoldb, make sure that we do not have duplicates and learn how to export the data we gathered... 
<!--more--> 

The code for this project can be found in [stackjobs repo on github](https://github.com/gyurisc/stackjobs).

### Installing mongodb

I need to store the data somewhere and I choose to use mongodb, because of no particular reason. :) Let's install it via brew by using the commands: 

``` bash
brew update
brew install mongodb
```

If you want to download the install package or use some other methods to install, please visit the following page [Install MongoDB Community Edition on OS X
](https://docs.mongodb.com/master/tutorial/install-mongodb-on-os-x/) and choose the method that suits you. 

Then create the directory structure for mongoldb in a suitable location and start the server 

``` bash 
mkdir mongodb/data/db
mongod --rest --dbpath mongodb/ 

```
You can confirm that the mongoldb is up and running by visiting [http://localhost:28017](http://localhost:28017).

#### How to configure mongo to run as a service? 
http://stackoverflow.com/questions/5596521/what-is-the-correct-way-to-start-a-mongod-service-on-linux-os-x

Because, I installed mongodb using brew I can also start and stop mongodb as a service on macOS with the following command: 

``` bash 
brew services start mongodb
brew services stop mongodb
brew services list
```

My only problem is with this approach is that I do not know how can I enable the --rest interface in this case, but navigating to [http://localhost:27017](http://localhost:27017) I get the standard mongodb warning, meaning that my database engine is running and that is good enough for now. 

In order, to communicate with mongodb in python, you will need to install pymongo module. Install the module with the following command: 

``` bash 
pip install pymongo
```

### Saving the items in db  

To be able to save the items gathered you will need to add some configuration to your project and also some code to your pipelines.py. 

#### Setting up a Pipeline
First, let’s see how the pipeline works. We will define a new class called MongoDBPipeline. This class has a constructor and a process_item method that will be called whenever a new item is created, so we will be using the **process_item** to check the validity of the item first, then it will be checked against a list called **ids_seen**. This list contains all the items that we already processed. We will ensure this way that there will be  no duplicate	d items in our database. 

If the item is valid and we have not seen it yet then it is now ready top be added to our mongo database. 

``` python validating and adding the item to our mongodb
    def process_item(self, item, spider):
        
        valid = True
        for data in item:
            if not data:
                valid = False
                raise DropItem("Missing {0}!".format(data))
      
        if valid and item['jobid'] not in self.ids_seen:
            self.collection.insert(dict(item))
            self.ids_seen.add(item['jobid'])            
            log.msg("Job added to MongoDB database!",
                    level=log.DEBUG, spider=spider)
        else:
            raise DropItem("Job id {0} already exists!".format(item['jobid']))    
            
        return item        
```

Now, we need to check out the constructor for this class. Here we initialise the connection and fill **ids_seen** list with the    ids that we already have in the database. 

``` python initialising database connection and fill up our id list
import pymongo
import sys

from scrapy.conf import settings
from scrapy.exceptions import DropItem
from scrapy import log

class MongoDBPipeline(object):
    def __init__(self):
        connection = pymongo.MongoClient(
            settings['MONGODB_SERVER'],
            settings['MONGODB_PORT']
        )
        db = connection[settings['MONGODB_DB']]
        self.collection = db[settings['MONGODB_COLLECTION']]
        self.ids_seen = set()
        for job in self.collection.find():
            self.ids_seen.add(job['jobid'])
        
        print "Number of jobs in database"
        print len(self.ids_seen)

```
#### Configuring mongodb connection. 

We need to do a little more work in order to be able to connect to the database and to register the newly added MongoDBPipeline, so it is actually called. In the settings.py, we need to added either add or modify the following line, so it looks like this: 

``` python 
ITEM_PIPELINES = {'stackjobs.pipelines.MongoDBPipeline' : 300, }

MONGODB_SERVER = "localhost"
MONGODB_PORT = 27017
MONGODB_DB = "stackoverflow"
MONGODB_COLLECTION = "jobs"

```

With the first line, we register our pipeline class on the system and assign a priority. With this mechanism, we can specify	in what we order we want the pipelines to be executed, if we would have more than one pipelines. 

The for lines after that are specifying how to connect to the database, such as the name of the server and server port, database name and collection name (table name).

With these changes we can now run our parser and all items scraped will be stored in our mongo database.

### Exporting data from mongo 

What to do next? Maybe, we want to export the data to a file, so we can do further analysis and cleaning up on it. For this, I used the mongoexport command with the following parameters to create a csv file

``` bash exporting data to csv
mongoexport --db stackoverflow --collection jobs --type csv --fields jobid,title,employer,location,salary,description,tags,url,date --out stackoverflow_jobs.csv
```

It is also possible to export the data in son format, if needed.  For that, you can use the following command: 

``` bash exporting data to son 
mongoexport --db stackoverflow --collection jobs --type json --fields jobid,title,employer,location,salary,description,tags,url,date --out stackoverflow_jobs.json
```

### Scheduling our job  

The last thing we need to do is to schedule our scraper to run on my mac. This can be achieved using crontab. I will schedule my scraper to run in every 2 hours as I do not think that running it more often would make sense. 

First, you will need to start the nano editor to edit your crontab file: 

``` bash
env EDITOR=nano crontab -e
```

Now enter the following to run our scraper every 2 hours. Use Ctrl-0 and Ctrl-X to exit the editor and save. 

``` bash
0 */2 * * *  cd ~/dev/scrapers/stackjobs && /usr/local/bin/scrapy crawl Stackjob
```

To list the existing crontab jobs, we can use the following command: 

``` bash listing crontab jobs 
crontab -l 
``` 
### Summary 

In this part, We have installed mongodb on our machine and added logic and configuration to the scraps project, so we can save all items that has been found to the database. Also, We have added a way to export the data into CSV or JSON file format from the mongo database. Finally, added this script to the crontab job and scheduled to run it every 2 hours. 

In the next part, I will spend a little time on the exported data and use pandas to transform the exported data into a a better consumable format. 
 
### Additional Information:

- [Schedule jobs with crontab on Mac OS X]( https://ole.michelsen.dk/blog/schedule-jobs-with-crontab-on-mac-osx.html)
- [Unix & Linux crontab every examples](http://alvinalexander.com/linux/unix-linux-crontab-every-minute-hour-day-syntax)
