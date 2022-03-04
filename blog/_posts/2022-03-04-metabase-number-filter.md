---
layout: post
title: Using Number Filters in Metabase  
description: A work around to slider-like filters in Metabase.
tags: metabase
---

![](/asset/screenshot/2022-03-04-metabase-number-filter-img03.jpg)

A requirement from one of my clients was to be able to filter by prices in Metabase. Intuitively, I thought of a slider-like filter in Power BI. However, such filter does not exist in Metabase.

But here is a work around: creating two number filters, one for minimum and one for maximum, with user-defined inputs. Say we would like to create a new question displaying sales count, filtered by the price paid for the sale. We start by converting the question into SQL.

    {%raw%} 

    SELECT count(*) AS "count"
    FROM "public"."sales"

    {%endraw%}

Then, we create two conditions in the `WHERE` clause, using Metabase variables. For this example, I would be using the variables *price_min* and *price_max*.

    {%raw%} 

    SELECT count(*) AS "count"
    FROM "public"."sales"
    WHERE pricepaid >= {{price_min}}
    and pricepaid <= {{price_max}}

    {%endraw%}


Now go to **Variables** > *price_min* > **Variable Type** > *Number* > **Required?** > Turn on. Under **Default filter widget value**, you will include something sensible for your needs. For this example, a minimum price of 0 would work for this example. Repeat the same steps for the maximum price. Keep in mind, the query will not compile unless there are valid default values.

![](/asset/screenshot/2022-03-04-metabase-number-filter-img01.jpg)

Once you have included the card to a dashboard, go to **Edit dashboard** > **Add a filter** > **Equal to** > **Label** > type *Minimum Price* > on the card itself, **Column to filter on** > *price_min* > **Done**. Repeat the same steps for the maximum price filter and save changes. 

![](/asset/screenshot/2022-03-04-metabase-number-filter-img02.jpg)

As the user, you now have the option to input the minimum and maximum values to the number filter.