---
layout: post
title: 'Working with dbt in VS Code'
subtitle: 'Some Helpful Extensions and their Features'
description: 'tbd'
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

Subscribers of dbt-cloud enjoy some great features when developing data warehouses (DWH). 

For the rest of us who work on dbt in VS code, there are extensions which make development a smooth task. 

The following some of the most useful extensions and their features.

### [CSV Rainbow](https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv)

Whenever we are working with seeds, this externsion comes in handy. With it, we are able to quickly open and modify the csv files which the seeds are based on. 


![2024-01-17-dbt-vscode-extension-img01](/images/2024-01-17-dbt-vscode-extension-img01.jpg){:loading="lazy"}
<font size="-1"><center><span> A CSV file opened with the CSV Rainbow extension installed </span></center></font>
<br>


CSV Rainbow also has additional features which allow you to query the CSV file itself. Like if it was one more table in your DWH. 


### [Query Results in dbt Power User](https://marketplace.visualstudio.com/items?itemName=innoverio.vscode-dbt-power-user#querypreview)

Probably the most well-known VS Code extension among analytics engineers, dbt power user is a must have tool. 

Among its many features, this extension allows us to preview results from resources we are creating. 


![2024-01-17-dbt-vscode-extension-img02](/images/2024-01-17-dbt-vscode-extension-img02.jpg){:loading="lazy"}
<font size="-1"><center><span> An example view of the Query Preview feature </span></center></font>
<br>


Your models, more likely than not, will include Jinja placeholders. This prevents you from running the model-in-progress directly from the CLI against your DWH. 

This feature takes the model-in-progress and compiles the Jinja placeholders to run a preview of our model's results. 

While using this tool, the user can always specify a limit of results to show, hence limiting DWH resource consumption. 

Please, keep in mind that this feauture unfortunately does not work with all dbt adapters. The preview query automatically places a `limit 100` towards the end of the compiled query. 

For this reason, the preview feature does not work with the MSSQL adaptor and derivatives. 


### [Create and Edit Documentation in dbt Power User](https://marketplace.visualstudio.com/items?itemName=innoverio.vscode-dbt-power-user#gendoc)

One of the reasons why documentation falls out of our priorities is because of the number of steps between our work and the documentation itself. 

After finishing their model, the user would normally to the dbt project's models folders, and look for the righ schema YAML file to document the model in. If a new schema file needs to be created, then the user would normally get stuck at the "how do you format one of these once again?". It is really inconvenient. 

![2024-01-17-dbt-vscode-extension-img03](/images/2024-01-17-dbt-vscode-extension-img03.jpg){:loading="lazy"}
<font size="-1"><center><span> An example view of the documentation feature </span></center></font>
<br>


With this feature, we are able to write documentation on the go while working on the model. We are able to write and edit documentation for not only the model itself, but also one and each of the model's column


### [Lineage in dbt Power user]()

The visuals of model lineage is one of the most powerful tools at the time of modeling in a DWH. Feature normally would tell us whether there are redundancies in our DAG or help us choose the right materialization for our model. 

Normally, to reach to model lineage, the user would have to run a `dbt docs generate` and `dbt docs serve`, once again, putting extra steps in between the engineer's work and their tools. 

![2024-01-17-dbt-vscode-extension-img04](/images/2024-01-17-dbt-vscode-extension-img04.jpg){:loading="lazy"}
<font size="-1"><center><span> An example view of the lineage feature </span></center></font>
<br>


With this feature, we are able to bypass the above steps and quickly see model lineage while working or our model. The feature initiall shows the user a couple of models in the model lineage. Simply press on the **+** symble to see further nodes in the DAG. 

### Final Thoughts

The abovementioned features are only a fraction of the tools available to dbt users in VS Code. dbt Power User itself has many more fantastic features that engineers should considered. For example, column lineage and documentation generation come immediatedly to mind. To use these however, the user must first reliquish some project-related data to 

With this feature, we are able to 


