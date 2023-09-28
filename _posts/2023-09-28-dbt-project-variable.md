---
layout: post
title: 'Cool dbt Feature: Project Variables'
subtitle: 'Avoid Hardcoding Information Into Queries'
description: 'A brief introduction to project variables and their benefit when modeling in dbt'
date: 2023-09-28 21:08:00 +0200
author: paulo
image: '/images/2023-09-28-dbt-project-variable-img00.jpg'
image_caption: 'Photo by [AltumCode](https://unsplash.com/@altumcode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/dMUt0X3f59Q?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)'
tags: [dbt, dwh, big-query]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@altumcode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">AltumCode</a> on <a href="https://unsplash.com/photos/dMUt0X3f59Q?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
--->
  

The most significant benefit of the [project variable](https://docs.getdbt.com/docs/build/project-variables) feature in [dbt](https://www.getdbt.com/) is that it enables us to avoid hardcoding information into our queries. Why is this advantageous? Well, consider the following scenario in the context of `dbt-bigquery 1.6.4`.

### example for context

Let's consider a sample play history of music data from [Last FM](https://github.com/moralescastillo/datasets/blob/main/play_history_df.csv). We'll use this data source to create a dimensional table that contains a unique list of tracks.

Now, our objective is to determine what qualifies as a "favorite" track. For our definition, we'll consider a track as a favorite if it has been played more than 50 times.

One way to approach this is to hardcode this definition directly into the query itself. Here's an example of how it might look:

<script src="https://gist.github.com/moralescastillo/3b4da3f6463d6431e78f87ad924cef0d.js"></script>
<font size="-1"><center><span> Query containing hardcoded definition of <code>if_favorite</code> </span></center></font>
<br>

<!---
https://gist.github.com/3b4da3f6463d6431e78f87ad924cef0d.git
-->

But what if, at some point, we decide to change the threshold for a "favorite" track to, say, 51 plays?

### introduce project variables

If we ever need to update the definition, we would have to modify the query in the model. But what if we're using this definition in multiple queries? Then making updates becomes tedious and prone to error.

To avoid the hassle of future changes, we use project variables. Simply define your project variable in the `dbt_project.yml` file:

<script src="https://gist.github.com/moralescastillo/cf24abe37f0ca19b73aa145b7671dc77.js"></script>
<font size="-1"><center><span> Defintion of project variable <code>fav_cut_off</code> in dbt_project.yml </span></center></font>
<br>

<!---
https://gist.github.com/cf24abe37f0ca19b73aa145b7671dc77.git
-->

Once the variable is defined, we can use the project variable function in the model's query:

<script src="https://gist.github.com/moralescastillo/fd9582519baf9792d658f1d17a53a679.js"></script>
<font size="-1"><center><span> Query containing dynamic definition of <code>if_favorite</code> using project variables </span></center></font>
<br>

<!---
https://gist.github.com/fd9582519baf9792d658f1d17a53a679.git
-->

After our `dbt run`, we can refer to the _target/compiled_ folder to confirm that the query matches our expectations.


### types of variables

Project variables can take multiple forms. For example, we can use intergers, strings and dates. 

<script src="https://gist.github.com/moralescastillo/b0f5cc8e07b1cea750920c1109cf4a96.js"></script>
<font size="-1"><center><span> Examples of intergers, strings and dates as project variables </span></center></font>
<br>

<!---
https://gist.github.com/b0f5cc8e07b1cea750920c1109cf4a96.git
-->

However, it's important to note that the value will be replaced by the Jinja placeholder. In the case of strings and dates, we still need to use quotations around the Jinja placeholder for the query to be valid.

<script src="https://gist.github.com/moralescastillo/c407af0d3838802ab352fbaa3d299e30.js"></script>
<font size="-1"><center><span> Query containing hardcoded definition of <code>if_favorite</code> </span></center></font>
<br>

<!---
https://gist.github.com/c407af0d3838802ab352fbaa3d299e30.git
-->

When in doubt, simply run the model containing project variables and check the results in project's _target/compiled_ folder. Does it look like you expected? Happy querying.

