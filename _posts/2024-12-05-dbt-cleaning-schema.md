---
layout: post
title: 'Cleaning Up Deprecated Models using dbt in Azure Synapse'
subtitle: 'Use Custom Macros to Get Rid of Old Tables and Views'
description: 'Use custom macros to remove deprecated tables and views from your target schema'
date: 2024-01-05 09:08:07 +0200
author: paulo
image: '/images/2024-01-05-dbt-cleaning-schema-img00.jpg'
image_caption: 'Photo by [Neal E. Johnson](https://unsplash.com/@neal_johnson?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/brown-and-black-brush-on-brown-wooden-table-V0cSTljC92k?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)'
tags: [dbt, dwh, azure, synapse]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@neal_johnson?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Neal E. Johnson</a> on <a href="https://unsplash.com/photos/brown-and-black-brush-on-brown-wooden-table-V0cSTljC92k?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
--->


When we create a model in a [dbt](https://www.getdbt.com/) project and execute it, a model is materialized in our target schema. If we later rename the model in our dbt project and execute it again, the old model is technically removed from our dbt project, but the old materialization persists in our Data Warehouse (DWH). Essentially, old models will remain in our target schema, consuming space in our DWH.

As a result, our target schema ends up with multiple deprecated tables and views, resources we no longer need. It's a good idea to remove them because they consume memory space in our DWH solution, incurring unnecessary costs. We can efficiently clean our target schema using custom macros and the `run-operation` command.

The following is a macro that accomplishes this task. It is a version of dan's answer on dbt's Discourse page ["Clean your warehouse of
old and depricated models"](https://discourse.getdbt.com/t/clean-your-warehouse-of-old-and-deprecated-models/1547/1). This version has been modified to work when using the `dbt-synapse` adapter.


<script src="https://gist.github.com/moralescastillo/43921d49bcf765af39fd76406592000f.js"></script>
<font size="-1"><center><span> macro <code>drop_old_relations</code> adapted for the context of Azure Synapse </span></center></font>
<br>

In addition to the DWH context-specific commands, we have added square brackets to the relation's name, in case the name contains white space.

For consistency, we suggest placing the above macro in the dbt project's `macros/` folder under the name `drop_old_relations.sql`. The file's name can be of your choice, but ensure that the macro's name and the file's name (without an extension) match.

To clean the target schema (the target dictated by our current profile), we run the following from the command line:

```bash 

dbt run-operation drop_old_relation

```

A dry run is also available, allowing us to preview tables and views that would potentially be dropped without actually executing the operation:


```bash 

dbt run-operation drop_old_relation --args '{dry_run = true}'

```

Happy querying. 

