---
layout: post
title: Pulling Azure Container Instance Logs into Azure Data Factory
description: tbd
tags: azure adf
---

![](/asset/screenshot/2023-01-05-pulling-logs-aci-img01.jpg)
<font size="-1"><center><span>Icons for Cloud Composer, Airflow, and Cloud Source Repository. </span></center></font>
<br>

Photo by <a href="https://unsplash.com/@atlaskadrow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Atlas Kadrów</a> on <a href="https://unsplash.com/photos/Cj5LEl85meM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  


Azure Container Instances (ACI) is one of Azure's on-demand cloud computing products. It allows the user to run docker containers on demand, in a serverless environment. It is also like Fargate in AWS and Cloud Run in GCP.

After running a docker container in ACI, the user is able to read the logs from the container’s portal page. Our goal now is to pull these logs into Azure Data Factory (ADF), and pass them to other activities in the pipeline.

## Web Activity in ADF

Start by creating a **Web** activity in a ADF pipeline. For this example, we name the activity *pull_aci_log*. Then, we use the List Logs ACI REST API:

    {%raw%}

    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ContainerInstance/containerGroups/{containerGroupName}/containers/{containerName}/logs?api-version=2022-10-01-preview

    {%endraw%}

adjusting with your ACI’s own information. Notice that, often times, containerGroupName and containerName are the same. You can find these pieces of information on the URL bar when visiting your ACI's portal page.

Then, place the above API url in the **URL box** and choose GET as **method**. In **Authentication**, select *System Assigned Managed Identity* and let https://management.azure.com/ be the Resource.

![2023-01-05-pulling-logs-aci-img02](/asset/screenshot/2023-01-05-pulling-logs-aci-img02.jpg)


## Granting Access to ADF from ACI

In order for it to pull the ACI logs, ADF must have appropitate access to the container. From the ACI portal page, click on **Access control (IAM)** > **Add role assignment**. 

![2023-01-05-pulling-logs-aci-img03](/asset/screenshot/2023-01-05-pulling-logs-aci-img03.jpg)

Choose the *Log Analytics Reader* role. This should give enough access to ADF to pull the logs. Then go **Next**.

![2023-01-05-pulling-logs-aci-img04](/asset/screenshot/2023-01-05-pulling-logs-aci-img04.jpg)

We then assign access to Managed identity and expand the **+Select** members option. Under Managed Identity, select *Data Factory (V2)* and find the respective factory. Click on the ADF member and then **Select**. Now click on **Next**.

![2023-01-05-pulling-logs-aci-img05](/asset/screenshot/2023-01-05-pulling-logs-aci-img05.jpg)

Finally, click on **Review + assign**.

## Testing the Web Activity


Back on the ADF Studio, it is time to test the pipeline holding the Web activity. From the ADF Studio's view on the pipeline, click **Debug** and check for results.

Did it fail? If the error is "The client '123xyz' with object id '123xyz' does not have authorization to perform action", then something went wrong with the role assignment step above. Give it another try. 

If access is correct, then you will see the logs under the name content in the output JSON.

![2023-01-05-pulling-logs-aci-img06](/asset/screenshot/2023-01-05-pulling-logs-aci-img06.jpg)

For this example, the logs would be accessible to other dependent pipeline activities via the dynamic parameter value **@activity('pull_aci_log').output.content**.

