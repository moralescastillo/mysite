---
layout: post
title: dbt run commands for model development 
description: A compilation of a few commands when developing new models in dbt
date: 2021-05-19 12:00:00 +0200
author: paulo
image: '/images/2022-05-19-dbt-run-command-img01.jpg'
image_caption: 'Logo of [dbt](https://www.getdbt.com/) (data base tool)'
tags: [dbt]
featured: 
---

When developing new models using dbt (data base tool), I often find myself using the following run commands:

### Run specific model

    {%raw%} 

    dbt run -–select model_alpha

    {%endraw%}

Instead of running the whole dbt project, the above command allows you to runs (and thus test) only the model you have just created. In the above example, the model to be run in `model_alpha`. To run more than one model, simply list their names in succession, e.g. `dbt run -–select model_alpha model_beta`.

### Run models downstream

    {%raw%} 

    dbt run –-select source:source_one.model_alpha+

    {%endraw%}

This command runs all models that are directly and indirectly dependent on the source table `model_alpha`, which makes part of `source_one`. In your project's *schema.yml* file, the source and model in the example would look like this:


    {%raw%} 

    - name: source_one
        description: This is my first source
        database: testingdb
        schema: sandbox
        tables:
        - name: model_alpha

    {%endraw%}

### Run models upstream and downstream

    {%raw%} 

    dbt run -–select +model_apha+

    {%endraw%}

As in the previous point, use the "+" sign as a prefix and/or suffix in order to run all models upstream and/or downstream from the model in question. The upstream models and downstream models are also referred to as a model's ancestors and descendants, respectively.

### Run specific models by tag

    {%raw%} 

    dbt run --select tag:user

    {%endraw%}

The above command will run all models with the tag `user`. Tags can be assigned on either a model's configuration options or a model's *dbt_project.yml* file. To run all those models associated with two tags, say the tags `user` and `test`, at the same time, then try  `dbt run –-select tag:user tag:test`.

For more on running specific models, refer to dbt's [documentation](https://docs.getdbt.com/faqs/running-models-downstream-of-source).