---
layout: post
title: Using Google Fonts with Plots in R
description: A short example on how to use Google Fonts within ggplot graphs
date: 2021-02-12 12:00:00 +0200
author: paulo
image: '/images/2021-02-12-google-font-r-img00.jpg'
image_caption: The R Project is free software for statistical computing
tags: [r, fonts]
featured: 
---


There is a plethora of fonts out there to make your plots more appealing to the eye. Thanks to the package [showtext](https://www.rdocumentation.org/packages/showtext/versions/0.9-2 ), you are able to use fonts from [Google Fonts](https://fonts.google.com/) on your plots.   

Remember that showtext does not work on RStudioGD. This implies that custom fonts will not show on your RStudio IDE. Instead, you can save the plot with the desired fonts in different supported formats.  

Here is an example on how to use the font Lobster in a plot saved as a PNG image.  


	{%raw%}
	# load libraries
	library(ggplot2)
	library(showtext)

	# add the font and turn showtext on automatically when using graphics
	font_add_google('lobster')
	showtext_auto()

	# create plot with iris data
	pp <-
	  ggplot(iris, aes(x=Sepal.Length, y=Sepal.Width)) +
		geom_point(shape=1) +
		ggtitle('Here, some Iris data') +
		theme(text = element_text(family = 'lobster')) # set the font for plot

	# call the plot
	pp

	# save the plot
	ggsave("showtext-example.png", plot = pp, width = 7, height = 4, dpi = 96)
	{%endraw%}

As mentioned, prompting the plot to appear in RStudio will show the default font.   

![2021-02-12-google-font-r-img01](/images/2021-02-12-google-font-r-img01.jpg)
 
Yet, the saved file will have the desired outcome. 

![2021-02-12-google-font-r-img02](/images/2021-02-12-google-font-r-img02.jpg)
 

References:

[Fonts not loading in showtext font_add_google](https://stackoverflow.com/questions/53219980/fonts-not-loading-in-showtext-font-add-google)
