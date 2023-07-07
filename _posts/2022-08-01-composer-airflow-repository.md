---
layout: post
title: Syncing a Cloud Source Repository to your Composer Airflow Environment
description: And thus starting your CI/CD pipeline
date: 2022-08-01 12:00:00 +0200
author: paulo
image: '/images/2022-08-01-composer-airflow-repository-img01.jpg'
image_caption: Logos for Cloud Composer, Airflow, and Cloud Source Repository
tags: [gcp, airflow]
featured: 
---

You created a brand new Cloud Composer environment. Now what? A natural next-step in the CI/CD pipeline is to sync a Git repository to your environment. 

The following example is based on the article [Sync a GitHub repo to your GCP Composer (Airflow) DAGs folder](https://engineering.adwerx.com/sync-a-github-repo-to-your-gcp-composer-airflow-dags-folder-2b87eb065915), but applied to the context of Cloud Source Repositories. 

Once started, your new environment has created a DAGs folder in a Cloud Storage bucket. Your goal is to sync the contents from your development DAGs folder in a Source Repository to the environment's DAG folder in Cloud Storage.

1. From your Composer environment, click on **Environment Configuration** and refer to the *DAGs Folder* location. Copy the link to the DAGs folder and save for later.

    ![2022-08-01-composer-airflow-repository-img02](/images/2022-08-01-composer-airflow-repository-img02.jpg)

2. Go to Cloud Source Repositories. Create a new repository by clicking on **Add repository**.

    ![2022-08-01-composer-airflow-repository-img03](/images/2022-08-01-composer-airflow-repository-img03.jpg)

3. Using your preferred authentication method, clone the repository to a local git repository.

    ![2022-08-01-composer-airflow-repository-img04](/images/2022-08-01-composer-airflow-repository-img04.jpg)

4. From GCP, go to Cloud Build. Click on **Triggers** > **Create trigger +**. Proceed to create a new trigger

    ![2022-08-01-composer-airflow-repository-img05](/images/2022-08-01-composer-airflow-repository-img05.jpg)

5. Under *event*, make sure the trigger is set to "Push to a branch." Moreover, select the repository created in step 2, and type `^master$` under *Branch*. 

    ![2022-08-01-composer-airflow-repository-img06](/images/2022-08-01-composer-airflow-repository-img06.jpg)

6. Under *configuration*, specify the path that will hold your yaml file, in this case `/cloudbuild/cloudbuild.yaml`.

    ![2022-08-01-composer-airflow-repository-img07](/images/2022-08-01-composer-airflow-repository-img07.jpg)

    and finalize the trigger by clicking on **Create**.

7. Now go to the local repository. Create two folders, one named `cloudbuild`, and another one named `dags`. Under `cloudbuild`, create the file `clouldbuild.yaml` containing the following content:

        {%raw%} 

        steps:
        - name: gcr.io/cloud-builders/gsutil
            args: ['-m', 'rsync', '-r', '-c', '-d', './dags', 'gs://your-composer-bucket/dags'] 

        {%endraw%}

    and replacing `gs://your-composer-bucket/dags` by your actual DAGs folder path from step 1. 

8. Under `dags`, create a test DAG. For this example, create the file `dag_test_01.py` with the following content:

        {%raw%} 

        from airflow import DAG
        from airflow.operators.bash import BashOperator
        from datetime import datetime, timedelta

        yesterday = datetime.now() - timedelta(days=1)

        default_args = {
            'owner': 'airflow',
            'depends_on_past': False,
            'start_date': yesterday,
            'retries': 0
        }

        dag =   DAG('dag_test_01', 
                default_args=default_args,
                schedule_interval= '0 0 * * *')

        t0 = BashOperator(
            task_id='say_hello',
            bash_command='echo "hello world"',
            dag=dag)

        t0

        {%endraw%}

9. Commit your changes in your local repository and push them to the Source Repository.

    ![2022-08-01-composer-airflow-repository-img08](/images/2022-08-01-composer-airflow-repository-img08.jpg)

10. Finally, go back to your Composer environment. Click on **Open Airflow UI**. Now you should see the test DAG from step 8 on the Airflow DAG panel.

    ![2022-08-01-composer-airflow-repository-img09](/images/2022-08-01-composer-airflow-repository-img09.jpg)

From now on, every commit pushed to the Source Repository will be reflected on your Cloud Composer environment. All related code is available on [GitHub](https://github.com/moralescastillo/code_sample/tree/main/composer_repository_sync).


