---
layout: post
title: Importing Excel Files from a Personal OneDrive in Python
description: I saw multiple people struggling with this topic on Stack Overflow. Here is simple and quick way of importing excel data located in a Personal OneDrive into Python
date: 2020-11-08 12:00:00 +0200
author: paulo
image: '/images/2020-11-08-excel-personal-onedrive-python-img00.jpg'
image_caption: OneDrive is Microsoft's cloud file storage solution
tags: [python, onedrive]
featured: 

---


Importing Excel files into python is very simple: 

1. Open the Excel file from the personal OneDrive. Go to **File** > **Share** > **Embed**. 

	![2020-11-08-excel-personal-onedrive-python-img01](/images/2020-11-08-excel-personal-onedrive-python-img01.jpg)

2. In the next Embed dialogue, click on **Generate**. 

	![2020-11-08-excel-personal-onedrive-python-img02](/images/2020-11-08-excel-personal-onedrive-python-img02.jpg)

3. In the Embed pane, refer to the **Embed code** box on the lower left-hand corner. Here you will find the embed iframe. Copy the iframe and paste it onto a text editor of your choice. 

	![2020-11-08-excel-personal-onedrive-python-img03](/images/2020-11-08-excel-personal-onedrive-python-img03.jpg)

4. Within the iframe, locate the values for **resid** and **authkey**.  

		{% raw %}
		<iframe width="402" height="346" frameborder="0" scrolling="no" src="https://onedrive.live.com/embed?resid=YOURRESID&authkey=YOURAUTHKEY&em=2&wdAllowInteractivity=False&wdHideGridlines=True&wdHideHeaders=True&wdDownloadButton=True&wdInConfigurator=True"></iframe> 
		{% endraw %}

5. With these values, use pandas to read the file. Replace 'YOURRESID' and 'YOURAUTHKEY' with the values found in the previous step.

		{%raw%}
		import pandas as pd

		resid = 'YOURRESID'

		authkey = 'YOURAUTHKEY'

		url_excel = ('https://onedrive.live.com/download?'
					 + 'resid=' + resid 
					 + '&authkey=' + authkey
					 + '&em=2&app=Excel')

		one_drive_df = pd.read_excel(url_excel)
		{%endraw%}

Beware that this method works for Excel files in a personal OneDrive, not enterprise OneDrive 