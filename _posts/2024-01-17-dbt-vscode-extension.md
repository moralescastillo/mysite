---
layout: post
title: 'Working with dbt in VS Code'
subtitle: 'Some Helpful Extensions and their Features'
description: 'A brief description of features from CSV Rainbow and dbt Power User, both extensions for VS Code'
date: 2024-01-17 13:00:00 +0200
author: paulo 
image: '/images/2024-01-17-dbt-vscode-extension-img00.jpg'
image_caption: 'Photo by [Zonduurzaam Deventer](https://unsplash.com/@zonduurzaam?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/a-close-up-of-a-red-light-on-a-white-device-BFNi3TWB2fw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)'
tags: [dbt, dwh]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@zonduurzaam?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Zonduurzaam Deventer</a> on <a href="https://unsplash.com/photos/a-close-up-of-a-red-light-on-a-white-device-BFNi3TWB2fw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
--->

Subscribers of [dbt-cloud](https://www.getdbt.com/product/dbt-cloud) enjoy some great features when developing data warehouses (DWH). For the rest of us who develop with dbt in VS Code, there are extensions that make development a much smoother task. The following are examples of some of the most helpful extensions and their features.

### [CSV Rainbow](https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv)

Whenever we are working with seeds, this externsion comes in handy. After installation, the user is able to quickly open, identify and modify the CSV file content located in `/seeds`, right from VS Code.  


![2024-01-17-dbt-vscode-extension-img01](/images/2024-01-17-dbt-vscode-extension-img01.jpg){:loading="lazy"}
<font size="-1"><center><span> A CSV file opened with the CSV Rainbow extension installed </span></center></font>
<br>

CSV Rainbow also enables the user to query the contents of a CSV file directly, just like if it were one more table in your DWH.

### [Query Results in dbt Power User](https://marketplace.visualstudio.com/items?itemName=innoverio.vscode-dbt-power-user#querypreview)

Probably the most well-known VS Code extension among analytics engineers, dbt Power User is a must-have tool. Among its many features, this extension allows the user to preview results from models on the go.

![2024-01-17-dbt-vscode-extension-img02](/images/2024-01-17-dbt-vscode-extension-img02.jpg){:loading="lazy"}
<font size="-1"><center><span> An example view of the Query Preview feature </span></center></font>
<br>

More likely than not, our models will include Jinja placeholders. This fact prevents us from running the model-in-progress directly from the CLI against our DWH. This feature then takes the model-in-progress and compiles it along with Jinja placeholders to run a preview of our model's results.

Moreover, while using this feature, the user can always specify a limit of results to show, hence limiting DWH resource consumption. Unfortunately, this feature does not work with the MSSQL adaptor and derivatives. 

### [Create and Edit Documentation in dbt Power User](https://marketplace.visualstudio.com/items?itemName=innoverio.vscode-dbt-power-user#gendoc)

One reason why documentation falls out of our priorities is because of the degrees of separation between our work and the documentation itself. For example, after finishing their model, the user would normally go to `/models`, and look for the right schema YAML file to document the model in.

If a new schema file needs to be created, then the user would frequently run into the question "how do you format one of these again?". It's silly.

![2024-01-17-dbt-vscode-extension-img03](/images/2024-01-17-dbt-vscode-extension-img03.jpg){:loading="lazy"}
<font size="-1"><center><span> An example view of the documentation feature </span></center></font>
<br>

Thanks to this feature, we are able to write documentation on the go while working on our models. We are able to write and edit documentation for not only the model itself but also for each of the model's columns.

### [Lineage in dbt Power User](https://marketplace.visualstudio.com/items?itemName=innoverio.vscode-dbt-power-user#lineage)

The visuals of model lineage are one of the most powerful tools at the time of modeling in a DWH. This feature normally would tell us whether there are redundancies in our DAG that we can avoid and/or help us choose the right materialization for our models.


Normally, to reach to model lineage, the user would have to run first `dbt docs generate`, and then `dbt docs serve`. Once again, a couple of degrees of separation between the engineer's work and their tools. 

![2024-01-17-dbt-vscode-extension-img04](/images/2024-01-17-dbt-vscode-extension-img04.jpg){:loading="lazy"}
<font size="-1"><center><span> An example view of the lineage feature </span></center></font>
<br>

With this feature, we are able to bypass the above steps and quickly see model lineage while working on our model. The feature initially shows the user a couple of models in the model lineage. Simply press on the **+** symbol to see further nodes in the DAG.

### Final Thoughts

The abovementioned features are only a fraction of the tools available to dbt users in VS Code. dbt Power User itself has many more fantastic features that engineers should consider. For example, column lineage and documentation generation come immediately to mind. To enable these, however, the user must first create an instance and request an API key from the extension's creator [Altimate AI](https://www.altimate.ai). The features come at no cost for single users. Happy querying.

