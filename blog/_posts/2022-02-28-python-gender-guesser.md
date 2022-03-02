---
layout: post
title: Matching First Names and Gender using gender-guesser  
description: gender-guesser is a python package that allows you to match a first names and name origin to a gender.
tags: python
---

Recently, I had the task to assign a gender to group of customers for whom we did not have such information. For the task, I was provided with a list of unique first names and their residence ISO country codes. To match gender with each name-country combination, I used the [gender-guesser](https://pypi.org/project/gender-guesser/) package. 


    {%raw%} 

    import pandas as pd
    import pycountry
    import pyodbc
    import gender_guesser.detector as gender

    '''
    import data
    '''

    server = 'servername'
    database = 'databasename'
    username = 'username'
    password = 'password'
    conn = pyodbc.connect('DRIVER={ODBC Driver 17 for SQL Server};'
                        'SERVER='+server+
                        ';DATABASE='+database+
                        ';UID='+username+
                        ';PWD='+ password)

    my_query = '''
    SELECT 
        first_name,
        country,
        freq
    FROM 
        customer.first_name_table
    '''

    df = pd.read_sql(my_query, conn)

    print(df)
    print(type(df))

    {%endraw%}

I began by transforming country ISO codes into country names with the help of `pycountry`. I also replaced blank spaces by underscores, as required by `gender-guesser`.  

    {%raw%} 

    '''
    find country names
    '''

    df['country_name'] = [pycountry.countries.get(alpha_2=ii).name.lower().replace(' ', '_') for ii in df['country']]

    {%endraw%}

The package only works for certain [countries](https://github.com/lead-ratings/gender-guesser/blob/master/gender_guesser/detector.py), otherwise it raises an error. Hence, I used first-name-country-name combinations when the country is found, else only the first name.


As per documentation, gender-guesser will return either “unknown (name not found), andy (androgynous), male, female, mostly_male, or mostly_female. The difference between andy and unknown is that the former is found to have the same probability to be male than to be female, while the later means that the name wasn't found in the database.”

For most names, the results are consistent at first glance. However, some results can be counter intuitive. For instance, d.get_gender('Zoë', 'denmark') results in andy while d.get_gender('Zoë') results in female. Zoë is a common female name (link).

Thus, I created a second version of the gender results using only the customers’ first name. 


![](/asset/screenshot/2021-02-24-scaler-mlflow-databricks-img01.png) 

To retrieve the model, we use the function `load_model()`.  

	{%raw%}	
	# load model saved under run id '124bf7c43bdf4733a0a17c5d4435da71'  

	run_id = '124bf7c43bdf4733a0a17c5d4435da71' 
	
	model_uri = 'runs:/' + run_id + '/logisticRegr'
	
	model = mlflow.sklearn.load_model(model_uri = model_uri) 
	{%endraw%}
 
To retrieve the scaler, we use the function `download_artifacts()`, as shown in the [documentation](https://www.mlflow.org/docs/latest/python_api/mlflow.tracking.html).  

	{%raw%}
	client = mlflow.tracking.MlflowClient() 

	local_dir = "/tmp/artifact_downloads" 

	if not os.path.exists(local_dir): 

		os.mkdir(local_dir) 
		
	local_path = client.download_artifacts('124bf7c43bdf4733a0a17c5d4435da71', '', local_dir) 

	scaler = open('/tmp/artifact_downloads/scaler.pkl', 'rb') 
	{%endraw%}

The same procedure can be applied to retrieve any metric, parameter or data set.