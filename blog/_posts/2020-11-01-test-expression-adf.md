---
layout: post
title: Testing Expressions in Azure Data Factory
description: here is some description
tags: adf dwh
---

Azure Data Factory supports the use of [expressions and functions](https://docs.microsoft.com/en-us/azure/data-factory/control-flow-expression-language-functions) to evaluate and fill in parameters and variables on the fly.

For instance, imagine we would like to execute part of a pipeline only when the job is run before noon. Instead of creating a new pipeline with a new trigger, we can use an **if condition** activity.

![](/asset/screenshot/2020-11-01-test-expression-adf-img01.png)

To use this type of activity, you will need to use expressions. If you are new to ADF, the expression syntax may not be the most straightforward.  

Instead of writing expressions by trial and error to see if the **if condition** works, you could first test an expression using the **set variable** activity.  

For instance, let's test an expression that checks whether the current time is before noon:  

1. Within a pipeline, create a **set variable** activity.   

	![](/asset/screenshot/2020-11-01-test-expression-adf-img02.png)

2. Within the pipeline, create a new variable. Make sure that the variable is set to the correct type. For our example, the expression outcome should be Boolean.  

	![](/asset/screenshot/2020-11-01-test-expression-adf-img03.png)

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

	![](/asset/screenshot/2020-11-01-test-expression-adf-img04.png)

5. Click on **Debug**.  

	![](/asset/screenshot/2020-11-01-test-expression-adf-img05.png)

6. Once the pipeline has run, go to the output.  
	
	![](/asset/screenshot/2020-11-01-test-expression-adf-img06.png)

7. Here you will see what the outcome of your expression is.  

	![](/asset/screenshot/2020-11-01-test-expression-adf-img07.png)

In case the pipeline did not run successfully, or it ran successfully but the outcome was not as expected, revise the expression and repeat. To see all available types of expression, check Micorsoft's [documentation](https://docs.microsoft.com/en-us/azure/data-factory/control-flow-expression-language-functions). 

 
 

 

 