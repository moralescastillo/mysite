---
layout: post
title: 'Creating Tables with LOBs using dbt in Azure Synapse: The Columnstore Index Error'
subtitle: 'What It Is and How To Go Around It'
description: 'A work-around to the default settings when creating tables using the dbt-synapse adapter'
date: 2023-09-17 22:50:00 +0200
author: paulo
image: '/images/2023-09-17-dbt-columnstore-index-img00.jpg'
image_caption: 'Results from a dbt run on the CLI'
tags: [dbt, dwh, azure, synapse]
feature:
---    


**TL;DR:** Explicitly change the index type in your model's configuration options.

Every dbt adapter is customized to the data warehouse solution they belong to. Not surprisingly, using a particular adapter captures particular errors. For this scenario, we discuss the columnstore index error in the context of `dbt-synapse 1.3.2`.

### the error

Picture a scenario where we have a dbt model with a column named `long_string` declared as a Large Object (LOB). 


<script src="https://gist.github.com/moralescastillo/64b9bc100791143c91f162fa045bb52c.js"></script>
<font size="-1"><center><span> <code>model_beta</code> materialized as a <code>view</code></span></center></font>
<br>

<!---
https://gist.github.com/64b9bc100791143c91f162fa045bb52c.git
-->


LOBs are a common data type when storing large binaries or character text data, e.g. URL links. A LOB type is created when casting a column as `nvarchar(max)` as in the above example.

When we run this model as a `view`, dbt compiles and executes it without any problems. 


<script src="https://gist.github.com/moralescastillo/7037963c8efc0ba47020bdd0d1461848.js"></script>
<font size="-1"><center><span> <code>model_beta</code> materialized as a <code>table</code></span></center></font>
<br>

<!---
https://gist.github.com/7037963c8efc0ba47020bdd0d1461848.git
-->

However, when we attempt to materialize the model as a `table`, we encounter an error in the CLI:
 
```
The statement failed. Column 'long_string' has a data type that cannot participate in a columnstore index
```

This error will also come up when using materializations of the type `incremental`.

### the root

A quick search on the internet will take you to the root of the problem. Firstly, Azure Synapse and similar solutions create tables using the `CLUSTERED COLUMN INDEX` as the default index. As per official documentation, "by default, dedicated SQL pool creates a clustered columnstore index when no index options are specified on a table."[^1]

At the same time, LOBs are not a good option when creating columnstore index index: "LOB data types (the (max) length data types) can't be the key of an ordered clustered columnstore index."[^2]

### the confusion

According to the `dbt-synapse` adapter documentation, "All configuration options for the Microsoft SQL Server adapter also apply to this adapter."[^3] 

Meanwhile, the `dbt-sqlserver` adapter documentation confirms the above default behavior by stating that "tables will, by default, be materialized as a columnstore tables."[^4]

The documentation also offers a solution by indicating that "this behaviour can be disabled by setting the `as_columnstore` configuration option to `False`."[^4] Accordingly, we modified the original model to account for this setting. 

<script src="https://gist.github.com/moralescastillo/023bd31101eb4d28172173ad26aac1da.js"></script>
<font size="-1"><center><span> <code>model_beta</code> with the option <code>as_columnstore</code> disabled </span></center></font>
<br>

<!---
https://gist.github.com/023bd31101eb4d28172173ad26aac1da.git
-->

However, the `dbt run` fails with the same error as before.


### the solution 

A quick inspection of the actual run script (located in our dbt project's target folder) reveals that the `CLUSTERED COLUMNSTORE INDEX` is still being called independently of whether `as_columnstore` is disabled.

<script src="https://gist.github.com/moralescastillo/232acd4e1d608b51e8087dd39ebdfd45.js"></script>
<font size="-1"><center><span> Compiled and run query from <code>model_beta</code> from the target folder</span></center></font>
<br>

<!---
https://gist.github.com/232acd4e1d608b51e8087dd39ebdfd45.git
-->


Then, the solution lies in explicitly declaring a different type of index that does not involve the column `long_string`. For example, we can specify dbt to create the table using a `HEAP` index.

<script src="https://gist.github.com/moralescastillo/d7dcfed4f249dd6af635c2b29a0519df.js"></script>
<font size="-1"><center><span> <code>model_beta</code> with the configuration option index set to <code>HEAP</code></span></center></font>
<br>

<!---
https://gist.github.com/d7dcfed4f249dd6af635c2b29a0519df.git
-->

This change in configuration makes the materialization of the above model possible. It is recommended to check what type of index fits your use case. 


[^1]: [Indexes on dedicated SQL pool tables in Azure Synapse Analytics](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-index)
[^2]: [CREATE COLUMNSTORE INDEX](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-columnstore-index-transact-sql?view=sql-server-ver16)
[^3]: [Microsoft Azure Synapse DWH configurations](https://docs.getdbt.com/reference/resource-configs/azuresynapse-configs)
[^4]: [Microsoft SQL Server configurations](https://docs.getdbt.com/reference/resource-configs/mssql-configs)



