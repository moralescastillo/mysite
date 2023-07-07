---
layout: post
title: Deriving Meaning from Date Features for Prediction in Python  
description: Some ideas on new features that can be created based on date features
date: 2021-02-26 12:00:00 +0200
author: paulo
image: '/images/2021-02-26-new-date-features-img00.jpg'
image_caption:  
tags: [machine-learning, python]
featured: 
---

Date features alone will not bring much to your predictive model.  Yet information from date features is often crucial for adding predictive power. To get to this information, date features need to be transformed into new features.  

Here are some ideas of new features you can derive from date features. 
 
	{%raw%}
	# load packages
	import datetime
	import pandas as pd

	# create sample data frame with dates
	df = pd.DataFrame()

	df['account_creation_date'] = (pd.date_range(start='2020-01-01', 
												 end='2020-07-01',
												 freq='D'))

	### Create new features

	# Create a feature of the number of days past since
	# account creation
	df['creation_to_now'] = ((datetime.datetime.now() 
							  - df['account_creation_date']).dt.days)

	# Create a feature based on day of week
	df['account_creation_dow'] = df['account_creation_date'].dt.day_name()

	# Create a feature based on the day number of the month
	df['account_creation_dom'] = df['account_creation_date'].dt.day.apply(str)
		
	# Create a feature based on month
	df['account_creation_month'] = df['account_creation_date'].dt.month.apply(str)

	# Create a feature based on year
	df['account_creation_year'] = df['account_creation_date'].dt.year.apply(str)
	{%endraw%}

The possibilities are countless.  

Date features are often overlooked and discarded in preliminary analysis. The truth is, predictability may be hidden on structural breaks and/or seasonality. 
