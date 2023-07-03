---
layout: post
title: Create Calendar Tables in Power BI
description: Tips on creating basic calendar tables.
tags: power-bi
---

A calendar table stores a sequence of unique dates between a start date and an end date. For simplicity in relations among tables, it is sensible to create a calendar table for each date column you would like to include in visualizations.

Say you have the table "account" with column *creation date*. To create a calendar table:

1. From Data Editor, go to **Table Tools** > **New tables**.

![](/asset/screenshot/calendar-table-img01.png)

2. On the header bar, use [CALENDAR()](https://dax.guide/calendar/) and pass starting *creation date* and ending *creation date* as inputs.  

		{%raw%}
		account calendar =
		CALENDAR ( MIN ( account[creation date] ), MAX ( account[creation date] ) )
		{%endraw%}
		
	The previous piece of DAX code creates the table "account calendar" with unique dates between the earliest and latest dates found in the column *creation date*. 

3. Mark the new table as a calendar table. Go to **Table tools** > **Mark as date table** > **Mark as date table**.

	![](/asset/screenshot/calendar-table-img02.png)

4. For a Date column, select the column *Date* and click **Ok**. 

	![](/asset/screenshot/calendar-table-img03.png)

Within calendar tables, you can further create other useful columns such as calendar week, month, semester and year. 

[comment]: <> (Finally, stack all useful columns into a hierarchy to be used in visualizations. )   