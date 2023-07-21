---
layout: post
title: 'dbt seed: Data Warehousing and CSV Files'
subtitle: 'Learnings from the analytics front lines'
description: 'The following are a few points I have learned while working with seeds in my dbt project'
date: 2023-07-21 22:05:00 +0200
author: paulo
image: '/images/2023-07-20-dbt-seed-csv-img00.jpg'
image_caption: 'Photo by [Tamanna Rumee](https://unsplash.com/ja/@tamanna_rumee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/FMhKAulD9A4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)'
tags: [dbt, dwh]
feature:
---  

In addition to sources and models, [dbt](https://www.getdbt.com/) allows us to use something called "seeds" in our project. Seeds are CSV files that we keep in the dbt project's *seeds* folder. They contain data that we use often but change rarely. For example, good candidates for seeds would be a list of test user IDs, local holiday dates, or codes mapped to labels.

Instead of hard-coding this data directly into a model, dbt lets us keep it in CSV format. We can then use it just like any other model in our project. One of the most frequently used seeds in my day-to-day is a mapping of German postal codes to cities and federal states.


### Adding a New Seed:

Let's begin from a development branch. Assume our CSV file is *postal_code_germany.csv*. To add a new seed, follow these steps:


1. Upload the CSV file in the **seeds** folder, that is, *seeds/postal_code_germany.csv*
2. Run the command `dbt seed`

<script src="https://gist.github.com/moralescastillo/3141a2ac1783ad97b49bd9e622d8695f.js"></script>
<font size="-1"><center><span> Successful load for seed <i>postal_code_germany</i> </span></center></font>
<br>

dbt will create a new table in our data warehouse target schema. Remember that `dbt seed` deletes the existing data in the table and adds all the lines from the CSV. This is different from `dbt run` on models with table materializations, which drops and creates tables from scratch.


### Updating an Existing Seed

If we need to update a seed with new data, we can follow these steps:

1. Modify the existing *seeds/postal_code_germany.csv* file and save it
2. Run `dbt seed` again to update the seed table with the new data

If we want to change the structure of the seed table, like renaming columns, adding new ones, or making other structural changes, we need to use `dbt seed --full-refresh` to apply the changes to our data warehouse.

### Merging into Production

After our changes have been run and tested in the development branch, we merge them into the production branch. When we want to make the changes to our seeds effective on our data warehouse, we run the dbt seed command again.

Chances are, you are running a build command on your production branch. Luckily, dbt build includes seeding seeds, so it takes care of the seed command for us. If we made structural changes to the seed table and want to update it in production, we must add the full-refresh flag, that is `dbt build --full-refresh`.

### Handling Column Types

CSV files are flexible but don't carry specific information about data types. This can cause issues, especially with postal codes. For example, if a postal code starts with a zero, like 04109 for Leipzig, our data warehouse will automatically treat it as an integer and drop the leading zero.

To fix this, dbt lets us specify column types for seeds. We can create a property file for seeds, similar to the ones we have for models, to set the correct data types.

<script src="https://gist.github.com/moralescastillo/6757ab31add692a71f9ceb305eb4a94e.js"></script>
<font size="-1"><center><span> Setting up column types on a property file for seed postal_code_germany </span></center></font>
<br>

The changes above are structural changes to the seed. This implies that we will need a full-refresh to make the changes applicable to our data warehouse.

Finally, don't forget that seeds can have specific properties. We can set tests, customize schema names, modify source paths and create documentation for seeds just like we do for models. For more information, check out the official documentation on [properties](https://docs.getdbt.com/reference/seed-properties) and [configurations](https://docs.getdbt.com/reference/seed-configs). Happy querying.


