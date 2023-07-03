---
layout: post
title: Running Scripts using Azure Data Factory and Batch, Part II
description: An example on how to run scripts within an Azure Data Factory pipeline.
tags: adf dwh
---

Now that Azure Batch and Azure Storage are linked to Azure Data Factory (ADF), let's create a pipeline:

1. From ADF Home, click on **Author** 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img01.png) 

2. Add a new pipeline by clicking on **+** > **Pipeline** 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img02.png) 

3. Name your pipeline. Go to **Activities** > **Batch Service** and drag the **Custom** activity into your pipeline  

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img03.png) 

4. Click on the Custom activity box and go to **Azure Batch**. From the drop-down menu, select the linked service to your batch account.  

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img04.png) 

5. Now go to **Settings**. The **Command** box acts as the terminal window. Here you type the command to be executed during the pipeline run. In our example, the command is `python helloWorld.py` (for R scripts, `Rscript helloWorld.R`)  

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img05.png) 

6. From the drop-down menu **Resource linked service**, select the linked service to your storage account. Click on **Browse Storage** and find the script container where *helloWorld.py* is located. 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img06.png) 

7. Test the pipeline by clicking on **Debug** 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img07.png) 

8. Check the status of the run in **Output** > **Status** 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img08.png) 

9. All run results are saved in the script container. Navigate back to the storage account main site, and go to **Blob service** > **Containers**.  A new container *adfjobs* has been created. Click on the container 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img09.png) 

10. In this container, you will find folders with output results from all runs related to this storage account/container. Click on the folder of any run and then click on the subfolder **Output** 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img10.png) 

11. You will find the files *stderr.txt* and *stdout.txt* which contain information about errors and output respectively. To inspect the output of the run, right click on *stdout.txt* and then select **View/edit** 

	![](/asset/screenshot/2021-03-08-running-script-adf-2-img11.png) 

Now that the pipeline is created and tested, it can be connected to triggers and other pipelines, and make part of a bigger orchestration process. 