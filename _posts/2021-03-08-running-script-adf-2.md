---
layout: post
title: Running Scripts using Azure Data Factory and Batch, Part II
description: An example on how to run scripts within an Azure Data Factory pipeline
date: 2021-03-08 12:00:00 +0200
author: paulo
image: '/images/2021-03-08-running-script-adf-2-img00.jpg'
image_caption: 
tags: [adf, dwh]
featured: 

---

Now that Azure Batch and Azure Storage are linked to Azure Data Factory (ADF), let's create a pipeline:

1. From ADF Home, click on **Author** 

	![2021-03-08-running-script-adf-2-img01](/images/2021-03-08-running-script-adf-2-img01.jpg) 

2. Add a new pipeline by clicking on **+** > **Pipeline** 

	![2021-03-08-running-script-adf-2-img02](/images/2021-03-08-running-script-adf-2-img02.jpg) 

3. Name your pipeline. Go to **Activities** > **Batch Service** and drag the **Custom** activity into your pipeline  

	![2021-03-08-running-script-adf-2-img03](/images/2021-03-08-running-script-adf-2-img03.jpg) 

4. Click on the Custom activity box and go to **Azure Batch**. From the drop-down menu, select the linked service to your batch account.  

	![2021-03-08-running-script-adf-2-img04](/images/2021-03-08-running-script-adf-2-img04.jpg) 

5. Now go to **Settings**. The **Command** box acts as the terminal window. Here you type the command to be executed during the pipeline run. In our example, the command is `python helloWorld.py` (for R scripts, `Rscript helloWorld.R`)  

	![2021-03-08-running-script-adf-2-img05](/images/2021-03-08-running-script-adf-2-img05.jpg) 

6. From the drop-down menu **Resource linked service**, select the linked service to your storage account. Click on **Browse Storage** and find the script container where *helloWorld.py* is located. 

	![2021-03-08-running-script-adf-2-img06](/images/2021-03-08-running-script-adf-2-img06.jpg) 

7. Test the pipeline by clicking on **Debug** 

	![2021-03-08-running-script-adf-2-img07](/images/2021-03-08-running-script-adf-2-img07.jpg) 

8. Check the status of the run in **Output** > **Status** 

	![2021-03-08-running-script-adf-2-img08](/images/2021-03-08-running-script-adf-2-img08.jpg) 

9. All run results are saved in the script container. Navigate back to the storage account main site, and go to **Blob service** > **Containers**.  A new container *adfjobs* has been created. Click on the container 

	![2021-03-08-running-script-adf-2-img09](/images/2021-03-08-running-script-adf-2-img09.jpg) 

10. In this container, you will find folders with output results from all runs related to this storage account/container. Click on the folder of any run and then click on the subfolder **Output** 

	![2021-03-08-running-script-adf-2-img10](/images/2021-03-08-running-script-adf-2-img10.jpg) 

11. You will find the files *stderr.txt* and *stdout.txt* which contain information about errors and output respectively. To inspect the output of the run, right click on *stdout.txt* and then select **View/edit** 

	![2021-03-08-running-script-adf-2-img11](/images/2021-03-08-running-script-adf-2-img11.jpg) 

Now that the pipeline is created and tested, it can be connected to triggers and other pipelines, and make part of a bigger orchestration process. 