---
layout: post
title: OpenStreetMap Features and Tags in R
description: Some tips to find the right features and tags for the elements you are mapping in R.
tags: r maps
---

The OpenStreetMap data package (`osmdata`) enables you to retrieve sf objects to plot maps. To add a map feature (e.g. a street, a river, a building), you select the feature key and its associated tag.  



If value is blank, all available tags associated to the given feature key will be retrieved. This could be unproductive as you do not always need everything that is available.  

Sometimes the tag is quite explicit. For instance, `key = 'water'` with `value = 'river'` retrieves information on rivers.

	{%raw%}
	# load package
	library(osmdata)

	# retrieve sf object for features on a map
	sf_object_water <- getbb("Berlin, Germany")%>%
	  opq()%>%
	  add_osm_feature(key = 'water', value = c('river')) %>%
	  osmdata_sf()
	{%endraw%}
 
 Yet, this value alone will not take into consideration canals, streams and ponds. Thus it is helpful to see available features and tags.

	{%raw%}
	# find (most of) features available
	available_features()

	# find all tags associtated to feature 'water'
	available_tags('water')
	{%endraw%}
 

To find out the feature and tag names associated to an element of a map:   

1. Find the location of interest in [www.openstreetmaps.org](https://www.openstreetmap.org/).  

	![](/asset/screenshot/2021-02-20-osm-feature-r-img01.png)
 
2. Right click on the map, and click on **Query Features**.  

	![](/asset/screenshot/2021-02-20-osm-feature-r-img02.png)

3. Hoover over the features on the right-hand side until the element you are looking for is highlighted in orange.  

	![](/asset/screenshot/2021-02-20-osm-feature-r-img03.png)
 
4. Click on the feature and see the associated tags.  

	![](/asset/screenshot/2021-02-20-osm-feature-r-img04.png)
 
Keep in mind that elements on a map may have more than one feature and tag name. For instance, the features *water* and *waterway* as very similar and are both available. 

