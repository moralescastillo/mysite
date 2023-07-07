---
layout: post
title: Datasets vs Reports in Power BI
description: And the importance of distinguish the one from the other.
date: 2020-11-03 12:00:00 +0200
author: paulo
image: '/images/2020-11-03-dataset-and-report-pbi-img00.jpg'
image_caption: Power BI is Microsoft's data visualization solution
tags: [power-bi]
featured: 
---

When you create a report in Power BI Desktop, you create two main components: A [dataset and a report](https://docs.microsoft.com/en-us/power-bi/fundamentals/service-basic-concepts).

### Dataset  

A dataset is a collection of data used by the report. It contains all information you compile in the Power Query, Data, and Model editors. This collection of data could be imported or connected to.   

The good thing about datasets is that they are reusable! Once you create a report in Power BI Desktop and publish it to Power BI Service, you can refer to the dataset for other reports.  

### Report  

A report is a collection of visualizations - that is, the collection of graphs, tables, and other visuals that you create in the Report editor. It is important to keep in mind that a report always relies on one dataset.  

Both reports and datasets can live in the same Workspace or in different Workspaces.  

![2020-11-03-dataset-and-report-pbi-img01](/images/2020-11-03-dataset-and-report-pbi-img01.jpg) 

When publishing a report from Power BI Desktop, both the report and the dataset will be published (unless you are reusing the dataset already). Once your report is in Power BI Service, datasets are located under **Datasets + dataflows** and reports are located under **Content**. To see all components, simply refer to **All**.  

![2020-11-03-dataset-and-report-pbi-img02](/images/2020-11-03-dataset-and-report-pbi-img02.jpg) 

When deleting a report from Power BI Service, remember that both components exist! If you delete the dataset, then all dependent reports will be deleted. But, deleting the report does not delete its underlying dataset.  

If a report can only refer to a dataset, can a report have many sources? Sure! The dataset itself can refer to many data sources, but it cannot refer to another dataset.  

Having these concepts in mind is important when using Power BI datasets as data marts. 
