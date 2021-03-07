---
layout: post
title: Running Scripts using Azure Data Factory and Batch, Part I
description: An example on how to connect Azure Batch and Azure Storage accounts to Azure Data Factory.
tags: adf dwh
---

A common job in orchestration is to run a python or R script within a pipeline. To achieve this, one can run scripts using Azure Data Factory (ADF) and Azure Batch.  

The following is an example on how to run a script using ADF and Azure Batch. Before starting, make sure you have and [batch account and a pool](https://docs.microsoft.com/en-us/azure/batch/batch-account-create-portal), and a [storage account]( https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal).  

Say you would like to run the script *helloWorld.py* in ADF using Azure Batch:  

	{% raw %}
	
	print('hello world')
	
	{% endraw %}

To run this script in a pipeline:  
 

1. From Azure Batch, go to **Blob service** > **Containers**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img01.png) 

2. Click on **+ Container**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img02.png) 

3. Name your new script container and click on **Create**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img03.png)
 
4. Access the script container  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img04.png)

5. Click on **Upload**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img05.png)
 

6. Locate the script *helloWorld.py* in your local folders and **upload**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img06.png)

7. Navigate to the ADF portal. Click on **Manage**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img07.png) 

8. Go to **Linked services** > **+ New**   

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img08.png) 
 

9. Under **Data store**, select **Azure Blob Storage** > **Continue**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img09.png) 
 
10. Name the linked service. Select the **Azure Subscription** under which the storage account was created. From the drop-down menu, select your **Storage account name** > **Create**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img10.png) 
 
11. Create a new linked service by going to **Linked services** > **+New**. Under **Compute** services. Select **Azure Batch** > **Continue**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img11.png) 

12. To find the required Batch information, refer to Azure Batch. Go to **Settings** > **Keys**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img12.png) 
 
13. Here you will find three of the fields required: **Account Name**, **Access Key** and **Batch URL**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img13.png) 
 
14. To find the **Pool Name**, refer to **Features** > **Pools** on the left-hand side menu from your main batch account site.  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img14.png) 
 
15. The **Pool ID** is the **Pool name**.   

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img15.png)

16. Go back to the ADF portal, and fill out the information required. Under **Storage linked service name**, select the linked service for the storage account created above. **Create**  

	![](/asset/screenshot/2021-03-07-running-script-adf-1-img16.png)
 
Now you have created the linked services to Azure Batch and Storage. In part II, you find the steps to create and run the pipeline. 

