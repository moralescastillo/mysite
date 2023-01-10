---
layout: post
title: Alerting for dbt Errors in Azure Data Factory Using Databricks
description: Stay on top of errors that may come up when running your dbt build.
tags: azure adf dbt databricks python
---

![](/asset/screenshot/2023-01-10-alerting-dbt-errors-img01.jpg)
<font size="-1"><center><span>Photo by <a href="https://unsplash.com/@dav420?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">David Pupaza</a> on <a href="https://unsplash.com/photos/heNwUmEtZzo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
 </span></center></font>
<br>

Is it me or dbt (getdbt.com) is synonym for Snowflake? The two are often seen hand in hand. In reality, dbt works with any other data warehouse solution. For some of our clients, we use dbt alongside Azure Synapse and Azure Data Factory (ADF).

For example, we use this neat approach by [Guang X](https://medium.com/@guangx/run-dbt-in-azure-data-factory-a-clean-solution-for-azure-cloud-edddf0c85849). It executes dbt via docker in a Azure Container Instance (ACI). The execution gets triggered by a ADF pipeline.

We expand on this solution by pulling the dbt logs from ACI. Then, we scan the logs for errors and send an alert in case any issue has come up. 

To follow along, make sure that your ADF pipeline is already pulling the logs from the ACI where dbt is running. Here is [an article](https://paulomoralescastillo.com/blog/2023/01/05/pulling-logs-aci.html) on how to achieve this step. 


### Capture the Logs

For this example, we use python and databricks as they work smoothly with ADF. But other solutions like Azure Functions or Azure Batch would also do the job.

From a databricks workspace, create a notebook with python as default language. To capture the dbt logs, we use the `dbutils` (databricks utilities) package. Let *dbt_results* be the name of the parameter containing the logs. Then, to capture this information, we would use:

    {%raw%}
    '''
    get dbt logs as parameter
    '''

    dbutils.widgets.text("dbt_results", "","")
    dbt_log = dbutils.widgets.get("dbt_results")

    {%endraw%}


After running this code, notice that databricks shows a box on the upper left-hand corner of the notebook. This box will come in handy when debugging the script.


### Scan for Errors

The following is a sample JSON response in ADF of dbt logs containing an error:


    {%raw%}

    {"content":"07:23:45 \n07:23:47 Concurrency: 1 threads (target='dev')\n07:23:47 \n07:23:47 1 of 4 START table model schema_stg.table_one ......................... [RUN]\n07:23:58 1 of 4 OK created table model schecma_stg.table_one .................... [\u001b[32mOK\u001b[0m in 11.11s]\n07:23:58 2 of 4 START table model schema_stg.table_two ................. [RUN]\n07:24:02 2 of 4 OK created table model schema_stg.table_two ............ [\u001b[32mOK\u001b[0m in 3.70s]\n07:24:02 3 of 4 START table model schema_stg.table_three .............. [RUN]\n07:24:24 3 of 4 OK created table model schema_stg.table_3 ......... [\u001b[32mOK\u001b[0m in 21.71s]\n07:24:24\r\n\r\n4 of 4 START test my_final_test .................. [RUN]\n07:24:30 4 of 4 FAIL 1 my_final_test ...................................... [\u001b[31mFAIL 1\u001b[0m in 3.36s]\n07:32:24 Finished running 3 table models, 1 tests in 123.45s.\n07:32:24 \n07:32:24 \u001b[32mCompleted with 1 error and 0 warnings\u001b[0m\n07:32:24 \n07:32:24 Done. PASS=3 WARN=0 ERROR=1 SKIP=0 TOTAL=4\n","ADFWebActivityResponseHeaders":{"Pragma":"no-cache","Vary":"Accept-Encoding","x-ms-request-id":"westeurope:pmc2023-f589-43a7-ba28-1c31c595a464","x-ms-ratelimit-remaining-subscription-reads":"1199","x-ms-correlation-request-id":"ba54ae03-9fb7-4ff8-a2d2-pmc2023","x-ms-routing-request-id":"SOMEWHERE:20230109T073510Z:ba54ae03-9fb7-4ff8-a2d2-pmc2023","Strict-Transport-Security":"max-age=31536000; includeSubDomains","X-Content-Type-Options":"nosniff","Cache-Control":"no-cache","Date":"Mon, 09 Jan 2023 07:35:10 GMT","Content-Length":"7278","Content-Type":"application/json; charset=utf-8","Expires":"-1"},"effectiveIntegrationRuntime":"someintegrationruntime","executionDuration":1,"durationInQueue":{"integrationRuntimeQueue":5},"billingReference":{"activityType":"ExternalActivity","billableDuration":[{"meterType":"SelfhostedIR","duration":0.016666666666666666,"unit":"Hours"}]}}

    {%endraw%}


Feel free to insert a JSON snippet like this in the box every time you would like to debug your python script. But make sure to delete it from the notebook after you are done debugging to script! 

![2023-01-10-alerting-dbt-errors-img02](/asset/screenshot/2023-01-10-alerting-dbt-errors-img02.jpg)

We then check if the dbt logs contain any errors:

    {%raw%}

    '''
    extract number of errors from logs
    '''

    import re
    regex = re.compile('ERROR=(\d+)')
    search_result = re.search(regex, dbt_log)

    if search_result:
        error_count = int(search_result.group(1))
    
    print(error_count)

    {%endraw%}

After checking for errors, we can e.g. send an email to the person in charge of the pipeline, alerting them of any issue. 


### Send the Email

To send an email from databricks, we use the `smtplib` package. The following is a sample of what the actual code would look like:

    {%raw%}

    '''
    if errors found, send email
    '''


    if error_count > 0:
    
        email_sender='sender_example@domain.com'
        email_sender_password='mypassword123'
        email_receiver='data_engineer_example@domain.com'
        
        import smtplib
        from email.mime.multipart import MIMEMultipart
        from email.mime.text import MIMEText

        s = smtplib.SMTP(host='smtp-mail.domain.com', port=587)
        s.starttls()
        s.login(email_sender, email_sender_password)

        msg = MIMEMultipart() 
        msg['From']=email_sender
        msg['To']=email_receiver
        msg['Subject']="Yikes! dbt got an error"
        msg.attach(MIMEText(dbt_log, 'plain'))

        s.send_message(msg)

        s.quit()


    {%endraw%}


Make sure you are using the right email host. Finally, add this notebook to ADF.

## Add the Notebook

Back on ADF studio, add a new **Databricks Notebook** activity to your pipeline. Connect it to the Web activity which pulls the dbt logs from ACI. Under **Azure Databricks** > **Databrinks linked service**, make sure you connect to the service that has your python script. Additionally, under **Settings** > **Notebook path**, browse for and select your python script. 

Under **Settings** > **Base parameters**, we add a new parameter named *dbt_results*. As value, we set the logs pulled from the previous activity using *@activity('pull_aci_log').output.content*.

![2023-01-10-alerting-dbt-errors-img03](/asset/screenshot/2023-01-10-alerting-dbt-errors-img03.jpg)

As always, make sure to hit **Debug** and check for any issues in the pipeline. The complete sample python script can be found in [GitHub](https://github.com/moralescastillo/code_sample/blob/main/alering_dbt_errors/alerting_dbt_errors.py).