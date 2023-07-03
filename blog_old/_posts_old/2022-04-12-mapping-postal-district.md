---
layout: post
title: Mapping of Postal and District Codes 
description: A mapping from Austrian 4-digit postal codes to Austrian 3-digit district codes
tags: dataset metabase maps
---

![](/asset/screenshot/2022-04-12-mapping-postal-district-img01.jpg)
<font size="-1"><center><span>Map of Austria at the district level, viewed from the Metabase Admin console. </span></center></font>
<br>

The [CSV file](https://github.com/moralescastillo/datasets/blob/main/austria-postal-district.csv) consists of a mapping from Austrian 4-digit postal codes to Austrian 3-digit district codes.

I created this dataset while working on a reporting task using Metabase. The requirements were to create a Metabase map, depicting user statistics in Austria, aggregated at the Austrian district level. The dataset at my disposal had the users' postal codes but no district codes. 

In Austria, the Gemeindekennziffer is a 5-digit string, used to identify municipalities. The first three digits of the Gemeindekennziffer is used to identify a municipality's district. Meanwhile, postal codes in Austria are 4-digit strings which are not related to the Gemeindekennziffer. 

I found the GeoJSON file of Austria at the district level in [this repository](https://raw.githubusercontent.com/ginseng666/GeoJSON-TopoJSON-Austria/master/2021/simplified-95/bezirke_95_geo.json) and Gemeindekennziffer information in [excel-karte.de](https://excel-karte.de/). With these two sources, I was able to create the mapping, and finally, the required report.