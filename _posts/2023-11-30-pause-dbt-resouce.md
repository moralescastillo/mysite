---
layout: post
title: 'Pausing dbt Resources after Pipeline Retirement'
subtitle: 'And Saving on Data Warehouse Storage and Computing Costs'
description: 'A summary of three ideas on pausing dbt resources: leveraging the exclusion flag, disabling resources, and transforming models into analyses'
date: 2023-11-30 14:00:00 +0200
author: paulo
image: '/images/2023-11-30-pause-dbt-resource-img00.jpg'
image_caption: 'Photo by [Namroud Gorguis](https://unsplash.com/@namroud?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/photo-of-black-and-brown-cassette-tape-FZWivbri0Xk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)'
tags: [dbt, dwh, bigquery]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@namroud?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Namroud Gorguis</a> on <a href="https://unsplash.com/photos/photo-of-black-and-brown-cassette-tape-FZWivbri0Xk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
--->


At a certain point in our journey as analysts, we would need to retire a data pipeline. Understandably, we would then like to pause the [dbt](https://www.getdbt.com/) resources (models, tests, seeds, snapshots) associated with this pipeline to conserve data warehouse storage and computing power. Our goal is to proceed without causing disruptions to the dbt project – a delicate process indeed.

In the context of dbt, a rushed approach could be to delete all soon-to-be-paused resources (.sql and .py) from the project. However, this is labor-intensive and prone to error. Instead, the following are some ideas on approaches in dbt when it comes to pausing and eventually retiring resources.

For the following examples, we use `dbt-bigquery 1.6.4`, with sample data on music from Spotify and Last.fm. The dbt project repository is available on [GitHub](https://github.com/moralescastillo/dbt_in_house).

### idea 1 -> --exclude

One method for pausing resources in dbt is to utilize the [exclude flag](https://docs.getdbt.com/reference/node-selection/exclude). Similar to `--select`, `--exclude` allows us to remove specific resources from our `dbt run` (or `test`, `seed`, `snapshot`, `build`). Using this flag, we can execute dbt while excluding the resources we intend to pause.

For example, imagine we would like to pause all models depending on the source table `bigquery.spotify_album` from a `dbt build`. Then we would run the command:

```bash 
dbt build --exclude source:bigquery.spotify_album+
```
We can also use [tags](https://docs.getdbt.com/reference/resource-configs/tags). For instance, by tagging all resources with 'legacy', we can exclude them from a dbt build:

```bash 
dbt build --exclude tag:legacy
```

One advantage of this approach is that the exclude flag also removes all tests associated with the resource. We can leave our resources in their respective folders, and it requires no changes to dependencies downstream from our paused resource. 

However, a drawback is the need to consistently include the exclude flag in the CLI command. This may require modifying existing jobs running on dbt, such as through containers.


### idea 2 -> enabled: false

Another way to pause dbt resources is to disable them by setting the [enabled configuration option](https://docs.getdbt.com/reference/resource-configs/enabled) to false. This approach essentially makes our dbt project consider the paused resources as non-existent.

To disable a resource, simply set `enabled: false` atop its .sql file as a configuration option. Alternatively, we can set the enable option at the project configuration level in `dbt_project.yml`. For example, if we wanted to disable all staging files located in `models/staging/spotify/`, we would include the following in the YAML configuration:

```yaml
models:
  my_project:
  	staging:
  		spotify:
			+enabled: false
```

One advantage of this approach is that it requires little modification, especially if our resources are well-organized into folders. It is also a versatile approach when the resource is an end-node, such as a production model without any downstream dependencies. Also, there are no modifications needed to the CLI command. 

Nevertheless, disabling resources becomes challenging when they have many dependencies downstream. For example, if one of our resources is listed in an _exposure_, we would have to remove the resource from the exposure.

### idea 3 -> analyses/

Another option for pausing resources is to turn them into [analyses](https://docs.getdbt.com/docs/build/analyses). For example, if we want to pause the model `stg_spotify_album`, we would move it from `models/staging/spotify` into `analyses/`.

One of the advantages of turning resources into analyses is that they will still compile and be found in the `target/compiled` folder. This allows us to use the compiled query and keep building upon the resource collaboratively. Also, no modifications to existing CLI commands are required. 

The clear disadvantage of this approach is manually moving all paused resources from `models/` to `analyses/`. Additionally, the dependency issue, similar to disabled models, will persist. We would need to move all downstream dependencies from our paused resource to `analyses/`.

The above are ideas on the possible steps to take to pause dbt resources. Eventually, we would like to migrate these resources out of your data warehouse, for which [model versioning](https://docs.getdbt.com/reference/resource-properties/versions) can be a good long-term approach.

There is no right or wrong when it comes to pausing models in dbt. All approaches have their pros and cons, which need to be analyzed on a case-by-case basis. Are there any other elegant ways to pause resources in dbt? Let us know in the comments. Happy querying.