---
layout: post
title: Connecting to Excel Files in OneDrive Personal from Power BI
description: A common but not intuitive task.
tags: [power-bi, excel]
date: 2020-04-25 12:00:00 +0200
author: paulo
image: '/images/2020-04-25-excel-one-drive-pbi-img00.jpg'
image_caption: Power BI is Microsoft's data visualization solution
tags: [power-bi]
featured: 
---

One might assume that connecting to Excel files from a Microsoft's personal OneDrive was an intuitive task. It is a simple task, but it is not very intuitive. 

To achieve the connection, start by navigating to the personal OneDrive via a browser of your choice.  
 
After locating and opening the excel file: 


1. Go to **File** > **Share** > **Embed** > **Generate**. 

	![2020-04-25-excel-one-drive-pbi-img00](/images/2020-04-25-excel-one-drive-pbi-img01.jpg)

2. In the lower left part of the prompted dialogue box, find the Embed code block.

	![2020-04-25-excel-one-drive-pbi-img02](/images/2020-04-25-excel-one-drive-pbi-img02.jpg)

3. Copy the code block and paste it onto a document editor of your choice (e.g. Word, notepad++). 

4. Now build a download URL to the excel file of the form 

		https://onedrive.live.com/download?resid=XXXXXXXX&authkey=XXXXXXXXXXXXXX&em=2&app=Excel

	where the actual values for *resid* and *authkey* are found in the code block from the previous step. Once the values are found, replace them in the link. Make sure there is no white spaces in your download URL. 

	![2020-04-25-excel-one-drive-pbi-img06](/images/2020-04-25-excel-one-drive-pbi-img06.jpg)
 
5. After the download URL is ready, create a **Web Connector** in your report. From Power BI Desktop, go **Home** > **Get Data** > **Web**. 

	![2020-04-25-excel-one-drive-pbi-img03.jpg](/images/2020-04-25-excel-one-drive-pbi-img03.jpg)

6. For URL, use the download URL built in the previous step. Click **Ok**. 
 
	![2020-04-25-excel-one-drive-pbi-img04](/images/e2020-04-25-excel-one-drive-pbi-img04.jpg)

7. Select the Excel sheet you would like to load and click **Load**. 

	![2020-04-25-excel-one-drive-pbi-img05](/images/2020-04-25-excel-one-drive-pbi-img05.jpg)

And you are done. You may wonder, why use a Web connector instead of an Excel connector? Both approaches will achieve the same goal when working from Power BI Desktop on your computer. The problem arises once the report is published to Power BI Service. In the service, the Excel connector will fail to find the local path to your Excel file. Meanwhile, the web connector will work regardless of the cloud service that is running the report. 