---
layout: post
title: Pulling Azure Container Instance Logs into Azure Data Factory
description: A description of how to include Azure Container Instance logs into your Data Factory pipeline.
date: 2023-01-05 12:00:00 +0200
author: paulo
image: '/images/2023-01-05-pulling-logs-aci-img01.jpg'
image_caption: 'Photo by [Atlas Kadrów](https://unsplash.com/@atlaskadrow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/Cj5LEl85meM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)'
tags: [azure, adf]
featured: 
---

Azure Container Instances (ACI) is one of Azure's on-demand cloud computing products. It allows the user to run docker containers on demand, in a serverless environment. It is also like Fargate in AWS and Cloud Run in GCP.

After running a docker container in ACI, the user is able to read the logs from the container’s portal page. Our goal now is to pull these logs into Azure Data Factory (ADF), and pass them to other activities in the pipeline.

### Web Activity in ADF

Start by creating a **Web** activity in a ADF pipeline. For this example, we name the activity *pull_aci_log*. Then, we use the List Logs ACI REST API:

    {%raw%}

    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ContainerInstance/containerGroups/{containerGroupName}/containers/{containerName}/logs?api-version=2022-10-01-preview

    {%endraw%}

substituting your ACI's own information for the replace strings. Notice that, often times, *containerGroupName* and *containerName* are the same. You can find these pieces of information on your browser's URL bar when visiting your ACI's portal page.

Then, place the above API in the **URL box** and choose *GET* as **method**. In **Authentication**, select *System Assigned Managed Identity* and let *https://management.azure.com/* be the **Resource**.

![2023-01-05-pulling-logs-aci-img02](/images/2023-01-05-pulling-logs-aci-img02.jpg)


At this moment, the activity is not ready to be debugged. In order for it to pull the ACI logs, ADF must have appropitate access to the container.

### Granting Access to ADF from ACI

From the ACI portal page, click on **Access control (IAM)** > **Add role assignment**. 

![2023-01-05-pulling-logs-aci-img03](/images/2023-01-05-pulling-logs-aci-img03.jpg)

Choose the *Log Analytics Reader* role. This built-in role gives enough access to ADF to pull the logs. Then, go **Next**.

![2023-01-05-pulling-logs-aci-img04](/images/2023-01-05-pulling-logs-aci-img04.jpg)

Following, we assign access to *Managed identity* and expand the **+Select members** option. Under **Managed Identity**, select *Data Factory (V2)* and find the respective factory. Click on the ADF member and then on **Select**. Then, click on **Next**.

![2023-01-05-pulling-logs-aci-img05](/images/2023-01-05-pulling-logs-aci-img05.jpg)

Finally, click on **Review + assign**. It is now time to debug the pipeline holding the Web activity.

### Debugging the Web Activity


Back on ADF Studio, go to the pipeline created earlier. Click on **Debug** and check the incoming results. Did the pipeline fail? If the error is "The client '123xyz' with object id '123xyz' does not have authorization to perform action", then something went wrong with the role assignment step above. Give it another try. 

If access is correct, then you will see the logs under the key *content* in the output JSON.

![2023-01-05-pulling-logs-aci-img06](/images/2023-01-05-pulling-logs-aci-img06.jpg)

The logs would then be accessible to subsequent dependent pipeline activities. The parameter which contains the logs would be *@activity('pull_aci_log').output.content* within the context of this example.

