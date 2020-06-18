---
layout: page
title: 5. Create Calendar Tables
---


For consistency in reports, a table dedicated to storing dates comes in handy. A calendar table allows you to use date hierarchies when using dates in visualizations.
 
A calendar table stores a sequence of unique dates between a start date and an end date. For simplicity in relation among tables, it is sensible to create a calendar table for each date column you would like to include in visualizations.

To create a calendar table, use [CALENDAR()](https://dax.guide/calendar/) and pass starting and ending dates as inputs. Take, for example, the table "account". This table has a date column account[creationDate]. From Power BI Desktop, go to Modeling -> New Table. On the header bar, we use:  

```

accountCalendar =
CALENDAR ( MIN ( account[creationDate] ); MAX ( account[creationDate] ) )

```

This piece of DAX code creates the table "accountCalendar" with unique dates between the earliest and latest dates found in the column account[creationDate].    

After creating a calendar table, make sure to do two things. First, mark the new table as a calendar table. From Power BI Desktop, in Data:  

 
 
* Click on the new calendar table  

* Table Tools > Mark as date table > Mark as date table  

* For a Date column, select the column name with unique dates  

* Click Ok  

In calendar tables, you can further create other useful columns such as calendar week, month, semester and year. Finally, stack all useful columns into a hierarchy to be used in visualizations. 

[comment]: <> (Add link to tip later on: include link to 056, include link to 057)   