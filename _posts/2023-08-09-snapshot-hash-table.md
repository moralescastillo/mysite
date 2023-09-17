---
layout: post
title: 'Taking snapshots using dbt in Azure Synapse: The Hash Distribution Issue'
subtitle: 'Troubleshooting and Solution for dbt Snapshots'
description: 'Learn how to troubleshoot and resolve the "Hash Distributed Table" error when taking snapshots using dbt in Azure Synapse Dedicated Pool'
date: 2023-08-09 16:29:00 +0200
author: paulo
image: '/images/2023-08-09-snapshot-hash-table-img00.jpg'
image_caption: 'Photo by [William Priess](https://unsplash.com/@william_priess?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/1jyHQxBAE7A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)'
tags: [dbt, dwh, azure, synapse]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@william_priess?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">William Priess</a> on <a href="https://unsplash.com/photos/1jyHQxBAE7A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
--->


[Dbt](https://www.getdbt.com/) is a powerful library used for data transformation and analysis. It supports various data warehouse solutions through different dbt adapters. Each adapter is designed to handle the unique syntax, functions, and configurations of specific data warehouses.

### The error

In this article, we will discuss a common error that occurs when using dbt with Azure Synapse Dedicated Pool. The error message looks like this:


```txt
18:11:08    ('42000', '[42000] [Microsoft][ODBC Driver 17 for SQL Server][SQL Server]Merge statements with a WHEN NOT MATCHED [BY TARGET] clause must target a hash distributed table.
```

The error is encountered while taking a snapshot of a table using the `dbt-synapse` adapter.

### Reproducing the Error

Let's understand how this error occurs. Imagine we have a model called `model_gamma`, which is a table containing columns like `id` and `modified_date`. To keep track of updates to this table, we create a [snapshot](https://docs.getdbt.com/docs/build/snapshots) named `snapshot_gamma`.

<script src="https://gist.github.com/moralescastillo/9996e7b74707961d053221a73ed99fb5.js"></script>
<font size="-1"><center><span> Query behind <code>snapshot_gamma</code> </span></center></font>
<br>

<!---
https://gist.github.com/9996e7b74707961d053221a73ed99fb5.git
-->

Initially, when we run the `dbt snapshot` command, it creates the first snapshot successfully. However, the error arises when trying to take another snapshot of `model_gamma`. This is because dbt attempts to modify the `snapshot_gamma` table, leading to the error.

### The Cause

The error is related to the use of the SQL `MERGE` command under the hood when taking snapshots with Azure Synapse. Specifically, the error occurs when using the `MERGE` command with a `WHEN NOT MATCHED BY TARGET` clause.

<script src="https://gist.github.com/moralescastillo/45cef0aaa5941855ab238c8d266c6cec.js"></script>
<font size="-1"><center><span> dbt-compiled query for <code>snapshot_gamma</code>, located in the project's target folder </span></center></font>
<br>

<!---
https://gist.github.com/45cef0aaa5941855ab238c8d266c6cec.git
-->

The `MERGE` command performs a full table scan and combines `INSERT`, `UPDATE`, and `DELETE` statements. The error suggests that if the `WHEN NOT MATCHED BY TARGET` clause is used, the target table (`snapshot_gamma`) must be hash-distributed.


### Round-Robin vs Hash 

Azure Synapse supports different distribution styles like Round-Robin and Hash. These are different approaches of storing data in the data warehouse.

The Round-Robin approach ensures data is evenly distributed across nodes. Each node gets an equal share of data, but data points with similar values might not end up in the same node.

The Hash approach uses a hash function (sort of a rule of thumb) to determine which node to save a data point in. Data points with equal values go into the same node, improving data locality and potentially benefiting certain queries like the `MERGE` statement.

### The Solution

To fix the error, we need to change the distribution style of the `snapshot_gamma` table to hash-distributed. We can pass specific configurations to our models and snapshots on Azure Sypanse thanks to `dbt-synapse`.

First, delete the original `snapshot_gamma` table:

```sql
drop table snapshot.snapshot_gamma;
```

Then, modify the snapshot file to indicate the desired distribution approach. That is, a HASH distribution along the id column:
 
<script src="https://gist.github.com/moralescastillo/6c26b07117df9eeb9ac87cc607a6096c.js"></script>
<font size="-1"><center><span> Query behind <code>snapshot_gamma</code>, after specifying a Hash distribution </span></center></font>
<br>

<!---
https://gist.github.com/6c26b07117df9eeb9ac87cc607a6096c.git
--->

After making these changes, run the `dbt snapshot` command again to create the first snapshot table. 

<script src="https://gist.github.com/moralescastillo/4aa2d530cc4bec43d6d11f8e20525d9a.js"></script>
<font size="-1"><center><span> Results from running <code>snapshot_gamma</code> from scratch</span></center></font>
<br>
<!---
https://gist.github.com/4aa2d530cc4bec43d6d11f8e20525d9a.git
--->
 
Finally, confirm that the snapshot works without errors when taken afterward.

<script src="https://gist.github.com/moralescastillo/ce858e47d0953dacae0ad4573f8336e6.js"></script>
<font size="-1"><center><span> Results from running <code>snapshot_gamma</code> a second time</span></center></font>
<br>
<!---
https://gist.github.com/ce858e47d0953dacae0ad4573f8336e6.git
--->

By understanding the error and the differences between Round-Robin and Hash distribution in Azure Synapse, we can effectively fix the issue and ensure smooth data processing with dbt. Happy querying.