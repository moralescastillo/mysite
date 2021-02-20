---
layout: post
title: Using Calendar Weeks in Power BI
description: Allowing months and years to be composed of exact weeks.
tags: power-bi
---

Often times it is useful to analyze the development of KPIs on a weekly basis. To achieve this, it is necessary to create a column with calendar weeks with some adjustments.  

Say you have the table "account calendar" with column *Date*:

1. From Data Editor on Power BI Desktop, select the table "calendar table."

	![](/asset/screenshot/use-calendar-week-img01.png)

2. Create *calendar week* using each date's *start of week* and *week number*.	

		{%raw%}
		account calendar =
		SELECTCOLUMNS (
			ADDCOLUMNS (
				ADDCOLUMNS (
					CALENDAR ( MIN ( account[creation date] ), MAX ( account[creation date] ) ),
					"start of week", [Date] - WEEKDAY ( [Date], 3 )
				),
				"week number", WEEKNUM ( [start of week], 2 )
			),
			"Date", [Date],
			"calendar week", IF ( [week number] < 10, "CW 0" & [week number], "CW " & [week number] )
		)
		{%endraw%}

Why use *start of week* instead of simply using *Date* to create the calendar week labels? When we use *Date* as a base for calendar week labels, we run into an issue of compatibility with other columns such as month. Months almost always have 4 weeks plus a couple of days more.  When using *start of week*, we guarantee that each month has either 4 or 5 weeks. In this way, we prevent visualizations from displaying duplicated data.