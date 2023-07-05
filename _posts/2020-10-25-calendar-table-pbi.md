---
layout: post
title: Create Calendar Tables in Power BI
description: Tips on creating basic calendar tables.
date: 2020-04-25 12:00:00 +0200
author: paulo
image: '/images/2020-10-25-calendar-table-pbi-img00.jpg'
image_caption: Power BI is Microsoft's data visualization solution
tags: [power-bi]
featured: 
---

A calendar table stores a sequence of unique dates between a start date and an end date. For simplicity in relations among tables, it is sensible to create a calendar table for each date column you would like to include in visualizations.

Say you have the table "account" with column *creation date*. To create a calendar table:

1. From Data Editor, go to **Table Tools** > **New tables**.

	![2020-10-25-calendar-table-pbi-img01](/images/2020-10-25-calendar-table-pbi-img01.jpg)

2. On the header bar, use [CALENDAR()](https://dax.guide/calendar/) and pass starting *creation date* and ending *creation date* as inputs.  

		{%raw%}
		account calendar =
		CALENDAR ( MIN ( account[creation date] ), MAX ( account[creation date] ) )
		{%endraw%}
		
	The previous piece of DAX code creates the table "account calendar" with unique dates between the earliest and latest dates found in the column *creation date*. 

3. Mark the new table as a calendar table. Go to **Table tools** > **Mark as date table** > **Mark as date table**.

	![2020-10-25-calendar-table-pbi-img02.jpg](/images/2020-10-25-calendar-table-pbi-img02.jpg)

4. For a Date column, select the column *Date* and click **Ok**. 

	![2020-10-25-calendar-table-pbi-img03.jpg](2020-10-25-calendar-table-pbi-img03.jpg)

Within calendar tables, you can further create other useful columns such as calendar week, month, semester and year. 

[comment]: <> (Finally, stack all useful columns into a hierarchy to be used in visualizations. )   