---
layout: post
title: "Playing with Pandas"
date: 2016-09-26 11:58:11 +0200
comments: true
categories: 
    - scrapy 
    - stackoverflow  
    - python 
    - pandas
    - jupiter notebooks
---
This is the third part of the articles I am writing about my little project I am working on. In part1, I created a web scraper to get the data I needed. In part 2, I added support to save the collected data to a mongodb database. Now in this part, I will look into how to clean up and add new features (columns) to the collected data to make it more suitable for analysis. 

My primary motivation here is to learn new technologies as I progress, so my baby steps may not be the state of art in this particular area and all tips and tricks or corrections are welcome. 

For this project I am using python and each day I love it more and more. There are some cool libraries for python such as pandas that will be used. There are some col tools such as python notebooks that will be also used. 

<!--more--> 

For starts, make sure that you have jupiter notebook installed on your machine and then start [Jupyter Notebooks](http://jupyter.org/) from the git repo folder. 

``` bash installing and starting jupiter notebook 
pip install jupiter
cd /path/to/stackjobs
jupiter notebooks
```

With this command, we started the iPython (Jupyter) notebooks and a new browser will be opened. Click on the [Enhancing and Extending data with Pandas](https://github.com/gyurisc/stackjobs/blob/master/notebooks/Enhancing%20and%20Extending%20data%20with%20Pandas%20.ipynb) notebook to see and run the code that this article with describe. Also the [enhance_data_with_pandas.py](https://github.com/gyurisc/stackjobs/blob/master/enhance_data_with_pandas.py) file contains the same code, so it can be run without iptyhon notebook. 

## Exploring data with pandas

First of all we need to import some libraries and make some changes to how the data is printed out. 

The changes in the display options e.g display.max_columns and display.max_rows are need in order for me to see the whole data when printed in the notebook and not just a few lines. Feel free to comment it out…

``` python importing libraries and setting options and then reading the data
import pandas as pd 
import numpy as np

# Let's change how printing the series works. I need to see all elements in the Series 
# source: http://stackoverflow.com/questions/19124601/is-there-a-way-to-pretty-print-the-entire-pandas-series-dataframe
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

jobs = pd.read_csv('data/stackoverflow_jobs.csv')
```

Next, there are three calls to see what kind of data we loaded into our _jobs_ data frame. We print out the columns, the types of the columns and some generic numerical data describing the dataset loaded. In the last one, the most important is the count that is telling us how many rows of data we loaded.

``` python 
jobs.columns # just show the columns 
jobs.dtypes
jobs.describe() # show me the row count
```

We can also print out the first and last few lines of the data, to have a look what we have using the _tail()_ and _head()_ method calls. 

``` python display the head and tail of the data
jobs.head()
jobs.tail()
```

## Working with Salary

In our jobs data, we have the _salary_ column that may or may not contains the salary is offered by the company for a particular position. When we have missing values in that column that can cause issues for further processing, so let’s replace the NA values with empty strings. 

``` python Replacing NA values with empty strings in the salary column
jobs.salary = jobs.salary.fillna('')
```  
Next, we will extract the information, if the given position offers equity or not and store it in a new column called _equity_.  

``` python Extracting equity
jobs['equity'] = jobs['salary'].str.contains('Provides Equity')
``` 

Next, we will move the salary data to a new series object and remove the _Provides Equity_ in order to make it easier to extract the other values. The reason for this is that I would like to leave the other columns as they were originally and do the needed modifications in the new columns or in some temp variables. 

``` python 
salary = jobs.salary.map(lambda x: x.replace("Provides Equity","").replace("/","").strip())
```

When we removed the _Provides Equity_ from the salary information our data is either an empty string or contains the currency information and the low high figure of the salary information. For example: 

```
€50,000 - 71,000
£35,000 - 65,000
$65,000 - 125,000
``` 

This information will be extracted using regular expression and captured in different columns. For the rows with empty values, it is recommended to provide a default value. Empty string for the currency and zero for the low-high figure. 

Then we map back the new information to the jobs data frame.  

``` python extracting and capturing currency and low-high figure. 
# salary = jobs.salary
salary = jobs.salary.map(lambda x: x.replace("Provides Equity","").replace("/","").strip())

sal = salary.str.extract('(?P<currency>[^\d]*)(?P<number_low>[\d,]+) - (?P<number_high>[\d,]+$)')

sal.number_low = sal.number_low.fillna(0)
sal.number_high = sal.number_high.fillna(0)
sal.currency = sal.currency.fillna('')

# mapping the new columns back to the original data frame 
jobs['currency'] = sal.currency
jobs['salary_low'] = sal.number_low
jobs['salary_high'] = sal.number_high

```

## Fixing up Location 

We will also need better location information, so we can do analysis by countries and cities. For this we need to extract country, state and city out of location column. 
But first let's remove the na values from location column. Then use a lambda to split the comma separated location value into individual fields.
Finally, rename the columns to city, location_1 and location_2. The reason for this is that the content of location can be different for different countries. 

```
London,UK
New York,NY
``` 

In the first expression the second member is a country and in the second expression it is a state, so we need to handle these differently. 

``` python split the location 
jobs.location = jobs.location.fillna('') # sometimes we have nothing in the location field. 

location_split = lambda x: pd.Series([i for i in x.split(',')])
locations = jobs['location'].apply(location_split)

locations.rename(columns={0:'city', 1: 'location_1', 2: 'location_2'},inplace=True)
``` 

### Fixing US locations
So it seems that US locations are special. They are in the form of city, state, we need this to be in form of city, state, country, so let's fix this first.
If we have a US state in _location1 column then put US in _location2. 

``` python Fixing US States 
us_states = ["AL", "AK", "AZ", "AR", "CA", "CO", "CT", "DC", "DE", "FL", "GA", 
          "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MD", 
          "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", 
          "NM", "NY", "NC", "ND", "OH", "OK", "OR", "PA", "RI", "SC", 
          "SD", "TN", "TX", "UT", "VT", "VA", "WA", "WV", "WI", "WY"]

locations['location_1'] = locations['location_1'].str.strip()
locations.loc[locations['location_1'].isin(us_states),'location_2'] = "US"
```

### Filling the state and country columns
We are now ready to put back the city, state and country information in our original data frame. 
Here is the logic, If in a row location_2 is null then location_1 contains the country of that location, if location_2 is not empty then location_2 is going to be the country and location_1 will contain the state.

``` python creating city, stat and country column
# if location_2 is null then location_1 column has the country 
# if location_2 is not null then location_2 has the country and location_1 contains the state 
jobs['country'] = np.where(locations['location_2'].isnull(), locations['location_1'], locations['location_2'])
jobs['state'] = np.where(locations['location_2'].notnull(), locations['location_1'], '')

jobs['city'] = locations['city']

# filling na for country 
jobs.country = jobs.country.fillna('')

# stripping spaces from new columns
jobs['city'] = jobs['city'].str.strip()
jobs['country'] = jobs['country'].str.strip()
```

Now we can see what countries are posting the most jobs. It seems that the US, Deutschland, Germany and the UK are the top countries. But wait. Aren't Germany and Deutschland are the same country? Let's fix this and some other countries with native names.

``` python replacing local country names with their english names
# replacing some of the country names with their english version 
jobs.loc[jobs['country'].str.contains('Deutschland'),'country'] = 'Germany' # Deutschland -> Germany
jobs.loc[jobs['country'].str.contains('Österreich'),'country'] = 'Austria' # Österreich -> Austria
jobs.loc[jobs['country'].str.contains('Suisse'), 'country'] = 'Switzerland' # Suisse -> Switzerland
jobs.loc[jobs['country'].str.contains('Schweiz'), 'country'] = 'Switzerland' # Schweiz -> Switzerland
jobs.loc[jobs['country'].str.contains('Espagne'), 'country'] = 'Spain' # Espagne -> Spain
jobs.loc[jobs['country'].str.contains('République tchèque'), 'country'] = 'Czech Republic' # République tchèque -> Czech Republic
jobs.loc[jobs['country'].str.contains('Niederlande'), 'country'] = 'Netherlands' # Niederlande -> Netherlands

jobs['country'].value_counts().head()
```

Now, we have a pretty neat table with data that we can do analytics on. In the next part, I will try to ask questions from  data and create some graphs to find out interesting stuff like: 

- What countries has the most jobs? 
- What cities has the most jobs? 
- What technologies are the most popular? 
- What technologies are paying the most? 

Coming soon… 