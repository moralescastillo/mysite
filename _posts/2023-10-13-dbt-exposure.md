---
layout: post
title: 'Cool dbt Feature: Exposures'
subtitle: 'They are much better than they sound'
description: 'A brief introduction to exposures and why they should be in every dbt project'
date: 2023-10-13 18:06:00 +0200
author: paulo
image: '/images/2023-10-13-dbt-exposures-img00.jpg'
image_caption: 'Photo by [Chris Liverani](https://unsplash.com/@chrisliverani?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/dBI_My696Rk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)'
tags: [dbt, dwh]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@chrisliverani?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Chris Liverani</a> on <a href="https://unsplash.com/photos/dBI_My696Rk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
  --->
  

When we first encounter the term *exposure* in the context of data, it doesn't sound all that appealing. In fact, it's natural to associate data exposures with the unwanted release of sensitive data. But let's not judge this feature by its name. In reality, [dbt](https://www.getdbt.com/) exposures are incredibly practical, and they offer many benefits to dbt developers. 

In a nutshell, an [exposure](https://docs.getdbt.com/docs/build/exposures) is a reference to a data product. This data product can take various forms, such as a dashboard, an application, or a machine learning pipeline. The data product depends on tables, views, and metrics, which are its dependencies, providing the necessary data for its proper functioning.

With exposures, developers can organize and document these dependencies efficiently. They provide an opportunity to explain the intended purpose of these data assets to others working one the data warehouse. Are these tables, views and metrics the backbone of a dashboard, or are they critical for an ML pipeline? How frequently should they be updated, and what's the downstream value of these data assets?

### as YAML files

Creating a dbt exposure is straightforward. We begin by creating a YAML file following the exposure template and its properties, and placing the file in the `models/` directory.

Within this YAML file, we set the exposure properties: its name, type, description, and an exposure owner, the person responsible for the data product.

<script src="https://gist.github.com/moralescastillo/14334eb41d3acb8fb006212398a90ae1.js"></script>
<font size="-1"><center><span> Example YAML for a dashboard exposure listing <code>dim_track</code> and <code>fct_play_history</code> as dependents. Using <code>dbt-bigquery 1.6.4</code>  </span></center></font>
<br>

<!---
https://gist.github.com/14334eb41d3acb8fb006212398a90ae1.git
-->

In this YAML file, we also list the exposure's dependencies, the set of tables, views and/or metrics on which the data product relies. By grouping these data assets into exposures, we can efficiently manage documentation and selection methods for different data products.

### as documentation pages

Running the `dbt docs generate` and `dbt docs serve` commands generate a dedicated page for exposures. On this page, the dbt developer provides insights into the data product and explains what is expected from its dependencies. For example, we can use tags like 'hourly' to indicate that the data assets from an exposure need to be built every hour.

![2023-10-13-dbt-exposures-img01](/images/2023-10-13-dbt-exposures-img01.jpg){:loading="lazy"}
<font size="-1"><center><span> A rendered documentation page for exposure <code>my_play_history_dashboard</code> </span></center></font>
<br>

This documentation page is not only a hub to understand the downstream usage of this group of data assets but also a gateway to access the data product itself. Using the *url* property in the YAML file, we can specify a link for users to navigate to the data product (see the button **View this exposure**).

### as selector method

Exposures also provide us with an endpoint for running and testing their dependencies. For instance, if our dashboard needs to be updated every hour, we do not have to run a complete `dbt build` every hour. Instead, we can materialize only those dependencies linked to the dashboard, as specified in the exposure. This approach helps us save time and computing resources.

In the case for exposure `my_play_history_dashboard`, we use the CLI command:

```bash

dbt build --select exposure:my_play_history_dashboard

```

However, when using an exposure as a selector method, it's crucial to ensure that the sources the exposure depends on are also kept up to date.

In large dbt projects, manually identifying the sources that feed into the tables and views belonging to the exposure can be time-consuming. In the case for exposure `my_play_history_dashboard`, we simplify this process in the CLI by using:

```bash

dbt ls --select +exposure:my_play_history_dashboard --resource-type source

```

This command lists all the sources that need updating before building the dependencies associated with the exposure.

In the end, exposures are not as negative as their name might suggest. On the contrary, they are one of the many valuable features dbt has to offer. They should be a part of every dbt project. So go ahead and make use of them. Happy querying.