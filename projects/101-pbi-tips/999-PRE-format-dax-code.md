---
layout: page
title: Format your DAX Code
---

As computer scientist Donald Knuth said, "Programs are meant to be read by humans and only incidentally for computers to execute." And this should also apply to your code within Power BI reports. 

That is why, when creating columns and measures, it is important to make sure your DAX code is properly formatted. This makes the code readable and easier to debug for you and your colleagues.  

For example, take the following measure: 


	Order Average Timeline = divide(CALCULATE(COUNT('Order'[Id]) + 0, filter(all('Account Calendar'), 'Account Calendar'[Date] <= max('Account Calendar'[Date]))), CALCULATE(COUNT('Account'[Id]) + 0, filter(all('Account Calendar'), 'Account Calendar'[Date] <= max('Account Calendar'[Date]))), blank()) 


A bit messy, right? To format this code:

1. Copy and paste your DAX code into SQLBI's [DAX formatting tool](https://www.daxformatter.com/). Click on **Format**. 

	![](/asset/screenshot/999-PRE-format-dax-code-img01.png) 

2. See the results 

		order Average Timeline =
		DIVIDE (
			CALCULATE (
				COUNT ( 'order'[Id] ) + 0,
				FILTER (
					ALL ( 'Account Calendar' ),
					'Account Calendar'[Date] <= MAX ( 'Account Calendar'[Date] )
				)
			),
			CALCULATE (
				COUNT ( 'Account'[Id] ) + 0,
				FILTER (
					ALL ( 'Account Calendar' ),
					'Account Calendar'[Date] <= MAX ( 'Account Calendar'[Date] )
				)
			),
			BLANK ()
		)
  
At the time of writing, I am not aware of a formatter for M code. Still, simple rules as indentation and limiting lines to a maximum of 80 characters improves the readability M code.  