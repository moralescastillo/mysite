---
layout: post
title: Saving and Loading Scalers using MLflow and Databricks with Python  
description: Chances are, you will be needing scalers to make predictions on new data sets
date: 2021-02-24 12:00:00 +0200
author: paulo
image: '/images/2021-02-24-scaler-mlflow-databricks-img00.jpg'
image_caption: Databricks is web-based platform for which provides automated cluster management while allowing you to work with Python, R and others, on IPython-style notebooks
tags: [databricks, machine-learning, python]
featured: 

---

MLflow is a platform to track experimentation, reproducibility, deployment of machine learning models. On the experimentation phase, mlflow facilitates the versioning of your models. It allows you to save not only the models but also metrics, parameters and datasets. For a quick introduction on mlflow, I found [this](https://www.youtube.com/watch?v=vqigwhYyJ7M) tutorial and [that](https://www.youtube.com/watch?v=OWJHHAtnAwY) tutorial to be very helpful.  

Say that, to create a model, you standardized a data set using some scaler. 

	{%raw%} 
	scaler = StandardScaler() 

	scaler = scaler.fit(X_train) 
	{%endraw%}
 
To make predictions on a new data set using this model, you will need not only the model but also the scaler. The scaler can be saved as a pickel object, using the package pickel.  

	{%raw%}
	#save the scaler locally using the package pickel 

	import pickel 

	pickel.dump(scaler, open('scaler.pkl', 'wb')) 

	# save the scaler alongside the model under your mlflow experiment 

	with mlflow.start_run(): 

		mlflow.sklearn.log_model(model_object, 'model’) 

		mlflow.log_artifact('scaler.pkl') 				
	{%endraw%}
 
On the databricks UI, a quick glance at the experiment’s latest run shows you both the model and the scaler. 

![2021-02-24-scaler-mlflow-databricks-img01](/images/2021-02-24-scaler-mlflow-databricks-img01.jpg) 

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