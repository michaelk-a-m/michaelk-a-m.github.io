---
id: 130
title: How to Make a COVID-19 Dashboard Using Tableau
date: 2020-05-23T18:04:48+10:00
author: michaelkam
layout: post
categories:
  - Dashboard
  - Data Visualisation
tags:
  - coronavirus
  - covid
  - covid-19
  - dashboard
  - tableau
---

A few weeks ago, I discovered that John Hopkins University (JHU) has made a public dataset on COVID-19 cases globally. In general, for each country and date, we have the following cumulative count of **confirmed cases**, **deaths** and **recovered**. Using Tableau Desktop, I developed a simple COVID-19 dashboard to show the prevalence and monitor the situation at the country level. 

## Merging the Datasets

If you have a look at the JHU&#8217;s Github repository, you notice that they have several time-series CSV files for the confirmed cases, deaths, and recovered separately. They also have daily reports in CSV files but we are not going to use them. Whilst you can link the three files using Tableau, but the format of each time-series files look like this.


|Country_Region|Province_State|22-Jan-20|23-Jan-20|…|24-Apr-20|
|--- |--- |--- |--- |--- |--- |
|Afghanistan|null|0|0|…|1,351|


Based on that formatting, we have to manually pivot each date columns and then link the three files based on Country, Province, and Date. However, the complication adds since the daily update will bring a new column each day. My solution for this is to use Python to merge these three files. I can run this on command prompt daily after pulling the raw CSV files from Github.

  
~~~ python
import numpy as np
import pandas as pd
from functools import reduce

##Read from the raw file of confirmed cases
ccse_df = pd.read_csv('.//csse_covid_19_data//csse_covid_19_time_series//time_series_covid19_confirmed_global.csv', header=0)
id_vars = ['Country/Region', 'Province/State', 'Lat', 'Long']
dates = list(set(ccse_df.columns) - set(id_vars))

##Transform the date columns
ccse_df_tf = ccse_df.melt(id_vars=id_vars, value_vars=dates, var_name='Date', value_name='Confirmed')
ccse_df_tf['Date'] = pd.to_datetime(ccse_df_tf['Date'])

##Read from the raw file of deaths
ccse_df_death = pd.read_csv('.//csse_covid_19_data//csse_covid_19_time_series//time_series_covid19_deaths_global.csv', header=0)
id_vars = ['Country/Region', 'Province/State', 'Lat', 'Long']
dates = list(set(ccse_df_death.columns) - set(id_vars))

##Transform the date columns
ccse_df_death_tf = ccse_df_death.melt(id_vars=id_vars, value_vars=dates, var_name='Date', value_name='Deaths')
ccse_df_death_tf['Date'] = pd.to_datetime(ccse_df_death_tf['Date'])


##Read from the raw file of recovered
ccse_df_rec = pd.read_csv('.//csse_covid_19_data//csse_covid_19_time_series//time_series_covid19_recovered_global.csv', header=0)
id_vars = ['Country/Region', 'Province/State', 'Lat', 'Long']
dates = list(set(ccse_df_rec.columns) - set(id_vars))

##Transform the date columns
ccse_df_rec_tf = ccse_df_rec.melt(id_vars=id_vars, value_vars=dates, var_name='Date', value_name='Recovered')
ccse_df_rec_tf['Date'] = pd.to_datetime(ccse_df_rec_tf['Date'])


#reduced
data_frames = [ccse_df_rec_tf, ccse_df_tf, ccse_df_death_tf]

#merged into one dataframe
df_merged = reduce(lambda left, right: pd.merge(left, right, on = ['Country/Region', 'Province/State', 'Date', 'Lat', 'Long'], how='outer', 
                                                ), data_frames)


df_merged.to_csv('.//datamodel//ccse_covid19.csv', header=True, index=False)
~~~

The result of the merged file looks like this.

|Country_Region|Province_State|Date|Confirmed|Deaths|Recovered|
|--- |--- |--- |--- |--- |--- |
|Afghanistan|null|22-Jan-20|0|0|0|



## COVID-19 Data Source Connection

S

## Building Tableau Worksheets



## Final COVID-19 Dashboard

The final result of the dashboard can be seen below embedded or through the [link](https://public.tableau.com/views/COVID19_15847816916750/CoronaDashboard?:display_count=y&publish=yes&:origin=viz_share_link).

I think it looks pretty good and could give me some helpful information about the development and basic statistics of the COVID-19 around the world or by country. 

If you are interested in my project for using linear regression to predict houses&#8217; sales price, please check this.
