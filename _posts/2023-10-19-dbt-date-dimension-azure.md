---
layout: post
title: 'Creating Date Dimensions using dbt in Azure Synapse: Custom Macros'
subtitle: 'And How to Make Them Dynamic'
description: 'A custom macro to create date dimensions when using the dbt-synapse adapter'
date: 2023-10-19 14:40:00 +0200
author: paulo
image: '/images/2023-10-19-dbt-date-dimension-azure-img00.jpg'
image_caption: 'Photo by [Kyrie kim](https://unsplash.com/@kyrie3?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/white-calendar-on-white-textile-jqxB3C0YNG0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)'
tags: [dbt, dwh, azure, synapse]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@kyrie3?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Kyrie kim</a> on <a href="https://unsplash.com/photos/white-calendar-on-white-textile-jqxB3C0YNG0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
  --->

Some [dbt](https://www.getdbt.com/) packages come equipped with useful macros for generating date dimension tables. However, these macros often face limitations when used with Azure Synapse or similar Microsoft data warehousing (DWH) solutions.

For instance, the `dbt_utils` package includes the `date_spine` macro, but it doesn't work as expected within Azure Synapse. This macro relies on a nested Common Table Expression (CTE), which isn't compatible with this environment.[^1]

Similarly, the `dbt_date` package provides the `get_date_dimension` macro, which seems very practical. Unfortunately, this package lacks support for Azure Synapse.[^2]

We can avoid these issues by writing our own macro. Below, you'll find a dbt macro that helps us create date dimension tables when using Azure Synapse and the likes.


### the dim_date_azure macro

Let's begin by creating the file 'dim_date_azure.sql' in the 'macros/' folder. The file will contain the following query, which allows us to generate a basic date spine between two dates:

<script src="https://gist.github.com/moralescastillo/a288665a00c3fbb4623fbd2ac67de75f.js"></script>
<font size="-1"><center><span> The <code>dim_date_azure</code> macro generates a series of dates</span></center></font>
<br>

<!---
https://gist.github.com/a288665a00c3fbb4623fbd2ac67de75f.git
-->


The above macro uses the internal system tables of our DWH to generate a table with multiple rows, which is then transformed into dates.

## the dim_date model

After saving the macro, we can use it to create a model in our `models/` folder. Since we anticipate using these dates alongside other production tables, it makes sense to materialize the model as a table among our other production tables.

For example, let's create a date dimension table spanning ten years, from 2015 to 2025:

<script src="https://gist.github.com/moralescastillo/8aed310f019614d241bd9778c5a09b45.js"></script>
<font size="-1"><center><span> The <code>dim_date</code> model builds a date dimension table using <code>dim_date_azure</code> macro</span></center></font>
<br>

<!---
https://gist.github.com/8aed310f019614d241bd9778c5a09b45.git
-->


After materialization, the resulting table will have a `d` column with a series of dates between the `start_date` and `end_date`.


### using it dynamically

We can also use the above `dim_date_azure` macro dynamically by using the `run_query` macro in the same model. For example, we can set `end_date` to be today's date plus six months:


<script src="https://gist.github.com/moralescastillo/b6c48c5964c36ca7bb211d5849ac010f.js"></script>
<font size="-1"><center><span> The <code>dim_date</code> model builds a date dimension table between a fixed start date and a dynamic end date</span></center></font>
<br>

<!---
https://gist.github.com/b6c48c5964c36ca7bb211d5849ac010f.git
-->


In this way, every time we materialize `dim_date`, the table will be updated with new dates accordingly. Happy querying.


[^1]: [WITH common_table_expression (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver16)
[^2]: [calogica dbt-date](https://github.com/calogica/dbt-date)
