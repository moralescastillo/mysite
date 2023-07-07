---
layout: post
title: Running Scripts using Azure Data Factory and Batch, Part I
description: An example on how to connect Azure Batch and Azure Storage accounts to Azure Data Factory
date: 2021-03-07 12:00:00 +0200
author: paulo
image: '/images/2021-03-07-running-script-adf-1-img00.jpg'
image_caption: Power BI is Microsoft's data visualization solution
tags: [adf, dwh]
featured: 
---

A common job in orchestration is to run a python or R script within a pipeline. To achieve this, one can run scripts using Azure Data Factory (ADF) and Azure Batch.  

The following is an example on how to run a script using ADF and Azure Batch. Before starting, make sure you have and [batch account and a pool](https://docs.microsoft.com/en-us/azure/batch/batch-account-create-portal), and a [storage account]( https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal).  

Say you would like to run the script *helloWorld.py* in ADF using Azure Batch:  

	{% raw %}
	
	print('hello world')
	
	{% endraw %}

To run this script in a pipeline:  
 

1. From Azure Batch, go to **Blob service** > **Containers**  

	![2021-03-07-running-script-adf-1-img01](/images/2021-03-07-running-script-adf-1-img01.jpg) 

2. Click on **+ Container**  

	![2021-03-07-running-script-adf-1-img02](/images/2021-03-07-running-script-adf-1-img02.jpg) 

3. Name your new script container and click on **Create**  

	![2021-03-07-running-script-adf-1-img03](/images/2021-03-07-running-script-adf-1-img03.jpg)
 
4. Access the script container  

	![2021-03-07-running-script-adf-1-img04](/images/2021-03-07-running-script-adf-1-img04.jpg)

5. Click on **Upload**  

	![2021-03-07-running-script-adf-1-img05](/images/2021-03-07-running-script-adf-1-img05.jpg)
 

6. Locate the script *helloWorld.py* in your local folders and **upload**  

	![2021-03-07-running-script-adf-1-img06](/images/2021-03-07-running-script-adf-1-img06.jpg)

7. Navigate to the ADF portal. Click on **Manage**  

	![2021-03-07-running-script-adf-1-img07](/images/2021-03-07-running-script-adf-1-img07.jpg) 

8. Go to **Linked services** > **+ New**   

	![2021-03-07-running-script-adf-1-img08](/images/2021-03-07-running-script-adf-1-img08.jpg) 
 

9. Under **Data store**, select **Azure Blob Storage** > **Continue**  

	![2021-03-07-running-script-adf-1-img09.jpg](/images/2021-03-07-running-script-adf-1-img09.jpg) 
 
10. Name the linked service. Select the **Azure Subscription** under which the storage account was created. From the drop-down menu, select your **Storage account name** > **Create**  

	![2021-03-07-running-script-adf-1-img10](/images/2021-03-07-running-script-adf-1-img10.jpg) 
 
11. Create a new linked service by going to **Linked services** > **+New**. Under **Compute** services. Select **Azure Batch** > **Continue**  

	![2021-03-07-running-script-adf-1-img11](/images/2021-03-07-running-script-adf-1-img11.jpg) 

12. To find the required Batch information, refer to Azure Batch. Go to **Settings** > **Keys**  

	![2021-03-07-running-script-adf-1-img12.jpg](/images/2021-03-07-running-script-adf-1-img12.jpg) 
 
13. Here you will find three of the fields required: **Account Name**, **Access Key** and **Batch URL**  

	![2021-03-07-running-script-adf-1-img13](/images/2021-03-07-running-script-adf-1-img13.jpg) 
 
14. To find the **Pool Name**, refer to **Features** > **Pools** on the left-hand side menu from your main batch account site.  

	![2021-03-07-running-script-adf-1-img14](/images/2021-03-07-running-script-adf-1-img14.jpg) 
 
15. The **Pool ID** is the **Pool name**.   

	![2021-03-07-running-script-adf-1-img15](/images/2021-03-07-running-script-adf-1-img15.jpg)

16. Go back to the ADF portal, and fill out the information required. Under **Storage linked service name**, select the linked service for the storage account created above. **Create**  

	![2021-03-07-running-script-adf-1-img16](/images/2021-03-07-running-script-adf-1-img16.jpg)
 
Now you have created the linked services to Azure Batch and Storage. In part II, you find the steps to create and run the pipeline. 

