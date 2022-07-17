---
layout: post
title: A simple Dockerfile for a dbt Synapse
description: A sample Dockerfile to containerize a dbt project with Azure Synapse dependencies
tags: dbt docker azure
---

![](/asset/screenshot/2022-07-18-Docker-dbt-synapse-img01.jpg)

<font size="-1"><center><span> Docker and dbt are indispensible tools for the CI/CD of modern DWH systems </span></center></font>
<br>

Creating a Dockerfile to containerize a [dbt](https://www.getdbt.com/) project is rather simple. There are many such sample files available on the internet.

The issue is that many of these samples assume the more popular dbt adapters such as postgres, redshift and BigQuery. I found no examples of containerized dbt projects using dbt adaptors for Azure Synapse.

So I created my own. I began by using the Fishtown Analytics's dbt image for Docker. Fishtown Analytics, the folks behind dbt, make their own dbt Docker image available by invoking 


    {%raw%} 
    FROM fishtownanalytics/dbt:1.0.0
    {%endraw%}

from the Dockerfile. However, as of this writing, there are two main obstacles with this approach. Firstly, the most recent dbt version available is v1.0.0. Secondly, this approach does not include the Synapse adapter. 

For these reasons, installing dbt and its adapters via pip from the Dockerfile becomes the only way to containerize a dbt project Synapse dependencies.

For this sample file, my requirements are

    {%raw%} 
    python:3.8.13
    dbt-core==1.0.5
    dbt-synaptse==1.1.0    
    {%endraw%}

where Synapse uses a ODBC Driver 17 for SQL Server. When building the Docker image with a draft Dockerfile, I first ran into common issues involving dbt and Windows. For example, the error


    {%raw%} 
    In file included from src/buffer.cpp:12:
    src/pyodbc.h:56:10: fatal error: sql.h: No such file or directory
    56 | #include <sql.h>
        |          ^~~~~~~
    compilation terminated.
    error: command 'gcc' failed with exit status 1
    {%endraw%}

occurs when installing `dbt-synapse`, as the `ggc` library is missing. Invoking the installation of the `unixodbc-dev` library solves this error. However, my `docker build` kept reporting that the ODBC Driver 17 for SQL Server was missing: 

    {%raw%} 
    Error: ('01000', "[01000] [unixODBC][Driver Manager]Can't open lib 'ODBC Driver 17 for SQL Server' : file not found (0) (SQLDriverConnect)")    
    {%endraw%}


I then installed the driver for Debian systems as per Microsoft [documentation](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-ver16#17). The following is the final draft Dockerfile that worked for my requirements:


    {%raw%} 
    FROM python:3.8.13

    # Update and install system packages
    RUN apt-get update -y && \
        apt-get install --no-install-recommends -y -q \
        git \
        libpq-dev \
        unixodbc-dev \
        python-dev && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

    # install ODBC Driver 17 for SQL Server
    RUN  apt-get update \
        && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
        && curl https://packages.microsoft.com/config/debian/11/prod.list > /etc/apt/sources.list.d/mssql-release.list \
        && apt-get update \
        && ACCEPT_EULA=Y apt-get install -y msodbcsql17

    # Install DBT
    RUN pip install -U pip
    RUN pip install dbt-core==1.0.5
    RUN pip install dbt-synapse==1.1.0

    ENV DBT_DIR /dbt
    ENV DBT_PROFILES_DIR=/dbt/profile/

    COPY . /dbt/
    WORKDIR /dbt/
    RUN dbt run
    
    {%endraw%}

The Dockerfile can be found in [GitHub](https://github.com/moralescastillo/code_sample/blob/main/Dockerfile). 
