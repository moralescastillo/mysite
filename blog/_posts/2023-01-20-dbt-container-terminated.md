---
layout: post
title: Is My dbt Ready?
subtitle: Waiting for a Containerized dbt Project to End in Azure Data Factory 
description: Set up activities in Azure Data Factory to track when your containerized dbt project has finished running.
tags: adf azure dbt dwh
---

![](/asset/screenshot/2023-01-20-dbt-container-terminated-img01.jpg)
<font size="-1"><center><span> Working with dbt (getdbt.com) within Azure Data Factory </span></center></font>
<br>

One of the advantages of using dbt (getdbt.com) is that we can use tags to run groups of models. In this way, we save time by not running all models available. 

This implies that some dbt runs could last shorter times than others. How would you go about making sure that a containerized dbt project has ended in a pipeline? 

### The Goal

In the past, we have talked about running dbt in Azure Data Factory (ADF) based on this [approach](https://medium.com/@guangx/run-dbt-in-azure-data-factory-a-clean-solution-for-azure-cloud-edddf0c85849). The solution consists on running dbt in a Azure Container Instance (ACI). A Web activity in ADF takes care of starting the container. And another activity waits until the container provisioning status is successful. 

When started, the container group receives the resources to run the container instance. Once this process is successful, the instance starts running. When the instance is done with the dbt run, it is terminated.

The earlier article covers how to start a containerized dbt project in ACI. Our goal now is to create two more activities to make sure that the ACI started and ended as expected. 

To achieve this, we will request the ACI’s status via REST API: 

    {%raw%}

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ContainerInstance/containerGroups/{containerGroupName}?api-version=2022-10-01-preview

    {%endraw%}
<font size="-1"><center><span> ACI GET REST API as found in <a href="https://learn.microsoft.com/en-us/rest/api/container-instances/container-groups/get?tabs=HTTP">Microsoft documentation</a></span></center></font>
<br>

Keep in mind that, ADF should have access rights to ACI to get a response from the state request. The steps on how to grant such access is in our previous article, [Pulling Azure Container Instance Logs into Azure Data Factory](https://paulomoralescastillo.com/blog/2023/01/05/pulling-logs-aci.html).


### Wait Until Success

We begin by creating an **Until** activity after the container-start activity. Let us call this activity *wait_aci_success*. 

![2023-01-20-dbt-container-terminated-img02](/asset/screenshot/2023-01-20-dbt-container-terminated-img02.jpg)
<font size="-1"><center><span> The Web and Until activities connected on success </span></center></font>
<br>

Within *wait_aci_success*, we create two activities. Firstly, a **Web** activity named *aci_state_if_success*. Secondly, a **Wait** activity named *wait_30_seconds_for_success*. 

As its name implies, we assign the Wait activity a 30-second waiting period.

![2023-01-20-dbt-container-terminated-img03](/asset/screenshot/2023-01-20-dbt-container-terminated-img03.jpg)
<font size="-1"><center><span> Set the waiting period to 30 seconds </span></center></font>
<br>

Within *aci_state_if_success*, we use the state ACI REST API mentioned above. Remember to substitute your ACI's own information for the replace strings.

We then place the above API in the **URL box** and choose *GET* as **method**. In **Authentication**, select *System Assigned Managed Identity* and let *https://management.azure.com/* be the **Resource**. 

![2023-01-20-dbt-container-terminated-img04](/asset/screenshot/2023-01-20-dbt-container-terminated-img04.jpg)
<font size="-1"><center><span> Include the ACI REST API in the Web activity </span></center></font>
<br>

The goal of these two activities is to repeatedly get the ACI state until it is successful. Remember that, *aci_state_if_success* returns a JSON string containing lots of information. 

For this step, we are interested in the provisioning state. It is available under the dynamic value `@activity('aci_state_if_success').output.properties.provisioningState`.

Now, we step back into the original pipeline. Click on *wait_aci_success* and go **Settings**>**Expression**>**Add dynamic content**. To check whether the provisioining state is successful, we evaluate the expression 

    {%raw%}

    @equals(
        'Succeeded', 
        coalesce(
            activity('aci_state_if_success').output.properties.provisioningState, 
            'null'))

    {%endraw%}
<font size="-1"><center><span> ADF dynamic content evaluating whether provisioning state is equal to 'Succeeded' </span></center></font>
<br>

within the expression builder.


![2023-01-20-dbt-container-terminated-img05](/asset/screenshot/2023-01-20-dbt-container-terminated-img05.jpg)
<font size="-1"><center><span> Include the expression within the Until activity's expression builder  </span></center></font>
<br>

Click **Ok**. At this point, we have a pipeline which starts the ACI and waits until the provisioning is successful. We now create an activity which checks that the instance has ended. 

### Wait Until Terminated

Right after *wait_aci_success*, connect another Until activity on success. Let us call this activity *wait_aci_terminate*.

This activity follows the same procedure as its predecessor, *wait_aci_success*. The only difference is that we are now interested, not in the provisioning state, but in the instance state. 

Let us use the name *aci_state_if_terminate* as the new status check activity within *wait_aci_terminate*. Also, let us assume that our container group only has one instance. The instance state would then be found under the dynamic value `@activity('aci_state_if_terminate').output.properties.containers[0].properties.instanceView.currentState.State`.

Within the expression builder from *wait_aci_terminate*, we evaluate whether the instance state in terminated:

    {%raw%}

    @equals(
        'Terminated', 
        coalesce(
            activity('aci_state_if_terminate').output.properties.containers[0].properties.instanceView.currentState.State, 
            'null'))

    {%endraw%}
<font size="-1"><center><span> ADF dynamic content evaluating whether the instance state is equal to 'Terminated' </span></center></font>
<br>

![2023-01-20-dbt-container-terminated-img06](/asset/screenshot/2023-01-20-dbt-container-terminated-img06.jpg)
<font size="-1"><center><span> Overview of the final ADF pipeline </span></center></font>
<br>

The outcome is an ADF pipeline that starts the ACI, checks if the ACI has started, and checks if the ACI has ended. In this way, we can orchestrate our next activity in the pipeline in a timely manner. 
