---
layout: post
title: Testing Expressions in Azure Data Factory
description: Testing expression is a simple trick in Azure Data Factory, so that you are able to see what is being passed on to your conditional activities.
date: 2020-11-01 12:00:00 +0200
author: paulo
image: '/images/2020-11-01-test-expression-adf-img00.jpg'
image_caption: Azure Data Factory is Microsoft's pipeline orchestration solution
tags: [adf, dwh]
featured: 
---

Azure Data Factory supports the use of [expressions and functions](https://docs.microsoft.com/en-us/azure/data-factory/control-flow-expression-language-functions) to evaluate and fill in parameters and variables on the fly.

For instance, imagine we would like to execute part of a pipeline only when the job is run before noon. Instead of creating a new pipeline with a new trigger, we can use an **if condition** activity.

![2020-11-01-test-expression-adf-img01](/images/2020-11-01-test-expression-adf-img01.jpg)

To use this type of activity, you will need to use expressions. If you are new to ADF, the expression syntax may not be the most straightforward.  

Instead of writing expressions by trial and error to see if the **if condition** works, you could first test an expression using the **set variable** activity.  

For instance, let's test an expression that checks whether the current time is before noon:  

1. Within a pipeline, create a **set variable** activity.   

	![2020-11-01-test-expression-adf-img02](/images/2020-11-01-test-expression-adf-img02.jpg)

2. Within the pipeline, create a new variable. Make sure that the variable is set to the correct type. For our example, the expression outcome should be Boolean.  

	![2020-11-01-test-expression-adf-img03.jpg](/images/2020-11-01-test-expression-adf-img03.jpg)

3. Prepare the expression you would like to test. For our example, the expression goes as follows.  
 
		{%raw%}
		@lessOrEquals(
			int(
				formatDateTime(
					convertFromUtc(utcnow(),  'W. Europe Standard Time'), 
					'HH'
				)
			), 
		12)
		{%endraw%}
  
4. Within the activity, go to **Variables**. For **Name**, choose the variable you created before.  For **Value**, paste the expression you created above. 

	![2020-11-01-test-expression-adf-img04.jpg](/images/2020-11-01-test-expression-adf-img04.jpg)

5. Click on **Debug**.  

	![2020-11-01-test-expression-adf-img05](/images/2020-11-01-test-expression-adf-img05.jpg)

6. Once the pipeline has run, go to the output.  
	
	![2020-11-01-test-expression-adf-img06](/images/2020-11-01-test-expression-adf-img06.jpg)

7. Here you will see what the outcome of your expression is.  

	![2020-11-01-test-expression-adf-img07](/images/2020-11-01-test-expression-adf-img07.jpg)

In case the pipeline did not run successfully, or it ran successfully but the outcome was not as expected, revise the expression and repeat. To see all available types of expression, check Micorsoft's [documentation](https://docs.microsoft.com/en-us/azure/data-factory/control-flow-expression-language-functions). 

 
 

 

 