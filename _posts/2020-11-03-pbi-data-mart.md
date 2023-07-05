---
layout: post
title: On the Reasons to Create Data Marts in Power BI
description: Saving you and your team priceless time when creating reports for your organization
date: 2020-11-03 12:00:00 +0200
author: paulo
image: '/images/2020-11-03-pbi-data-mart-img00.jpg'
image_caption: Power BI is Microsoft's data visualization solution
tags: [power-bi]
featured: 
---

A data mart is a central repository for all tables, formatting and relationships you need to create reports.  
 
With a data mart, there is no need to query data every time a new report is created. All required data for your reports is queried within the data mart already.   

![2020-11-03-pbi-data-mart-img01](/images/2020-11-03-pbi-data-mart-img01.jpg)
<font size="-1"><center><span> Power BI Setup Example without Data Mart  </span></center></font>
<br>

Using a data mart also guarantees consistency of definitions and data updates across reports.  

![2020-11-03-pbi-data-mart-img02.jpg](/images/2020-11-03-pbi-data-mart-img02.jpg)
<font size="-1"><center><span> Power BI Setup Example with Data Mart  </span></center></font>
<br>

Depending on the size of your organization and the data it handles, creating more than one data mart can be useful. For instance, you may create a data mart for sales while creating another for marketing.   

In Power BI, a dataset can be used as a data mart. To create a data mart in Power BI: 

1. Create a report with all tables intended for reporting. Make sure columns are formatted, and the correct relationships are created. No visualizations are needed. 

	![2020-11-03-pbi-data-mart-img03](/images/2020-11-03-pbi-data-mart-img03.jpg)

2. Publish the report by going to **Home** > **Publish**. Select the Workspace where the data mart will reside. Make sure to save the data mart under a meaningful/relevant name. 

	![2020-11-03-pbi-data-mart-img04.jpg](/images/2020-11-03-pbi-data-mart-img04.jpg)
	
That's it! To connect to the data mart when creating a new report: 

1. In a new report, go to **Home** > **Get data** > **Power BI datasets**.  

	![2020-11-03-pbi-data-mart-img05.jpg](/images/2020-11-03-pbi-data-mart-img05.jpg)

2. Select the data mart you are connecting to. Click **Create**. 

	![2020-11-03-pbi-data-mart-img06.jpg](/images/2020-11-03-pbi-data-mart-img06.jpg)

Make sure that your colleagues have read access level to the data mart. Keep in mind that this strategy only allows you to create measures in your report. To create new tables and columns, refer to the data mart itself. 