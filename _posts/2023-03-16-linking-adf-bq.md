---
layout: post
title: Linking to BigQuery in Azure Data Factory
subtitle: An example on how to use BigQuery as a data source in Azure Data Factory
description: In this article, we go over the steps, requirements and recommendations when creating a BigQuery linked service in Azure Data Factory 
date: 2023-03-16 12:00:00 +0200
author: paulo
image: '/images/2023-03-16-linking-adf-bq-img01.jpg'
image_caption: 'Working with Azure Data Factory and Google BigQuery'
tags: [azure, adf, gcp, big-query, dwh]
---
 
One advantage of Azure Data Factory (ADF) is its ability to integrate to a variety of data warehouses. Such integrations are called *linked services*. They are connections to an external data source or data destination.

In this example, we go over the requirements and steps to create a BigQuery linked service in ADF. We use a google service account and its key file in JSON format. Additionally, we use a self-hosted integration runtime installed on a windows virtual machine (VM).  

### Google Service Account

We begin by creating a [service account](https://cloud.google.com/iam/docs/keys-create-delete#iam-service-account-keys-create-console) with enough roles to access the BigQuery service. Then, we create a service account key file in JSON format.

![2023-03-16-linking-adf-bq-img02](/images/2023-03-16-linking-adf-bq-img02.jpg)
<font size="-1"><center><span> An example of a Google service account key file in JSON format </span></center></font>
<br>

Role assignment is a common topic when using service accounts to authenticate to BigQuery. It is important that the account has been given the correct roles to perform the tasks needed.

As a sanity check, we recommend testing if the account has the correct roles before moving onto ADF. For example, we can first use the key file to connect to BigQuery tables using VS Code.

### Self-Hosted IR

A [self-hosted integration runtime (IR)](https://learn.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime?tabs=data-factory) allows ADF to connect to specific network environments. It also allows access to files in the hosting machine. 

![2023-03-16-linking-adf-bq-img03](/images/2023-03-16-linking-adf-bq-img03.jpg)
<font size="-1"><center><span> An example of a self-hosted integration runtime connected from a Windows virtual machine </span></center></font>
<br>


In this scenario, the IR hosting machine is the place where we store our Google service account key file. The perfect place to save it in is the C drive. Otherwise, ADF would not be able to locate it and fail to authenticate. 

From the IR hosting machine, navigate to the C drive and save the key file.

![2023-03-16-linking-adf-bq-img04](/images/2023-03-16-linking-adf-bq-img04.jpg)
<font size="-1"><center><span> Saving a sample key file in the IR hosting machine's C drive </span></center></font>
<br>

### Creating a BigQuery Linked Service

From the ADF, navigate to **Manage**>**Linked services**>**+New**.

![2023-03-16-linking-adf-bq-img05](/images/2023-03-16-linking-adf-bq-img05.jpg)
<font size="-1"><center><span> Creating a new linked service in ADF </span></center></font>
<br>

In the *New Linked Service* window, search for and select **Google BigQuery** as the linked service.

![2023-03-16-linking-adf-bq-img06](/images/2023-03-16-linking-adf-bq-img06.jpg)
<font size="-1"><center><span> An example of a New Linked Service window in ADF </span></center></font>
<br>

In the *New Linked Service (Google BigQuery)* window, enter a name for your linked service. Under *integration runtime*, point to the self-hosted IR where the key file is stored. Include the Google *Project ID* and set *Request access to Google Drive* as **False**.  

Under *Authentication type*, select **Service authentication** and input the service account email. Remember, this information is found in the key file itself. 

![2023-03-16-linking-adf-bq-img07](/images/2023-03-16-linking-adf-bq-img07.jpg)
<font size="-1"><center><span> Sample BigQuery linked service properties </span></center></font>
<br>

Under *key file path*, point to the key file within the IR hosting machine. Next, **Test connection**; and finally, **Create**.

Pro tip: A successful test connection does not imply that a copy activity will succeed. When running into such issues, we recommend looking into IAM roles like [BigQuery.Read.Session.User](https://community.powerbi.com/t5/Service/BIgQuery-Account-Permissions/m-p/1404853).