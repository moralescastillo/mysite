---
layout: post
title: 'Surf, Work, and Explore: A Web Scraping Exercise'
subtitle: 'Using data to find the workation trifecta - surfing, affordability, and fast internet'
description: I scrape data using python to find new countries with surf spots where you can work remotely from, *ceteris paribus*
date: 2023-07-17 10:50:00 +0200
author: paulo
image: '/images/2023-07-15-surf-work-explore-img00.jpg'
image_caption: 'Photo by [frank mckenna](https://unsplash.com/@frankiefoto?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/eXHeq48Z-Q4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)'
tags: [python, remote-work]
feature:
---

One of the positive outcomes of the pandemic is the rise of remote work as a coveted company perk. In Germany, where I currently reside, it has become common for companies to offer employees the flexibility to work from outside the country.

This perk has opened up a world of opportunities for me and others to enjoy the thrill of surfing while working remotely. Whenever I plan my next outing, I search for the workation trifecta: Is there surf? Is it affordable? Is the internet fast?

In this article, I go on a data-driven exploration to learn about new countries with surf spots where I could work from the next time the opportunity arises.


### What is surf-able?

To my surprise, there are forty-four landlocked countries in the world, with only one in the Americas (sorry Bolivia). Does that mean you can surf in the rest of the world? I'm no surf pro, but my instinct says "not necessarily."

For instance, you can bring your board to the Caribbean coast of my native Colombia (leave the wetsuit at home), but chances are you won't encounter great waves all day long. The tides, wind, and sea floor simply don't create optimal conditions for surf swells.

To identify countries with good waves, I propose using the World Surf League (WSL) as a reliable proxy. In this exercise, I scrape a list of WSL championship tour stops from the past four years. While the WSL includes obvious destinations like Hawaii, it also features more remote places such as Japan, Peru, and Senegal.


## What is affordable?

To determine affordability, I turn to [Numbeo](https://www.numbeo.com/cost-of-living/), a crowd-sourced database that provides global price information. Visitors from around the world frequently share the prices of goods and services in their respective locations. 

Numbeo calculates various indices, including the cost of living, rent, and restaurant prices. All indices are relative to the prices in New York City, with an index of 100 representing the cost of living in NYC. Anything below 100 is cheaper, while anything above 100 is more expensive.

As a remote worker, your major expenses would be food and rent. Hence, I focus on Numbeo's *Cost of Living Plus Rent index*. Keep in mind that the index may have changed since the time of writing, as prices are continually updated.


### What is fast?

When I first experienced the internet speed at my Airbnb in Corralejo, I was taken aback. I assumed that such a fast *Fibra Optica* connection must come with a hefty price tag. However, I soon discovered that the entire island of Fuerteventura (and Spain in general) boasts accessible and fast internet connections.

Some countries, like Spain, are fortunate to have widespread access to fast internet. Back at home in Berlin, we are [not so lucky](https://www.npr.org/2019/01/03/678803790/berlin-is-a-tech-hub-so-why-are-germanys-internet-speeds-so-slow?t=1660816773204). This discrepancy is evident in global measurements of fixed broadband internet speeds.

To assess internet speed, I consulted SPEEDTEST's most recent *Global Median Speeds index*. While it comes as no surprise that countries like South Korea and the US rank high in speed, I was intrigued to see countries like Romania and Chile performing exceptionally well. And since you are wondering, Germany finds itself at a disappointing fifty-second position. *Egal*.


### The findings

To visualize the relationship between the cost of living index and median broadband download Mbps speed for each country, I created a plot. I divided the plot into four quadrants along the x-axis based on the median value. The goal was to identify countries located in the upper-left-hand corner, representing affordable countries with fast internet. I then narrowed down this selection to countries where good surfing conditions are likely.


![2023-07-15-surf-work-explore-img01](/images/2023-07-15-surf-work-explore-img01.jpg){:loading="lazy"}
<font size="-1"><center><span> Plot of countries according to the affordability, internet speed and possibility for sorfing spots </span></center></font>
<br>


The results yielded a mix of expected and surprising countries. Spain and Portugal, both well-known surf destinations, appeared in the coveted quadrant. I've had the pleasure of working and surfing from various spots in both countries, and it has always been an absolute delight.

In South America, Chile and Brazil stood out. With over 4000 km of coastline along the Pacific Ocean, Chile is renowned spots. Arica and Iquique being among some of the well-known ones. And Brazil, well, it's Brazil. Florianópolis is undoubtedly a must-visit. I will definitely keep these countries in mind for my future plans.

China emerged as the surprise. It's not a country that comes to mind when thinking of surfing. However, it turns out that the surfing scene in China is rapidly growing. After conducting some research, it seems that Hainan province is the [the place to be](https://medium.com/@theothermap/surfing-in-china-bc766c488190) for surf enthusiasts.

As remote work continues to flourish, the world becomes our office, and the possibilities for combining work and passion expand. So why not ride the waves of opportunity while embracing new cultures, all without compromising our professional commitments?
    
Checkout the code behind in [GitHub](https://github.com/moralescastillo/code_sample/tree/main/surf_work_explore). Happy coding.
