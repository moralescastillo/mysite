---
layout: post
title: Nudging The End User
subtitle: A PBI example on how to guide users towards optimal data usage while reducing the chances of leakage
description: In PBI, you can nudge the user towards selecting the data they need first, before it is actually displayed in the report.
tags: power-bi
---

![](/asset/screenshot/2023-02-19-nudge-the-user-img01.jpg)
<font size="-1"><center><span> Photo by <a href="https://unsplash.com/ko/@debrupas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Pascal Debrunner</a> on <a href="https://unsplash.com/photos/WuwKphhRQSM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a></span></center></font>
<br>
 
In [Nudge](https://www.goodreads.com/book/show/3450744-nudge), economist Richard Thaler promotes the practice of libertarian paternalism in society. By this idea, a choice architect designs ways to "nudge" others towards optimal decision-making. 

We would argue that, we, the creators of data products, are also choice architects. We are responsible for sharing data and insights in ways which are most practical to the end user. And thus, it is also our duty to nudge the end user to make good use of the data and insights given to them.

### The Use Case

A frequent use case of ours in Power BI (PBI) reports is table visuals listing information. The user would first apply filters to the table, and then export the resulting list into an excel file. This file would likely end up on the inbox of, let's say, an external partner later on. 

As choice architects, we provide the user with the information they need in PBI. But how do we nudge the user to export exactly the information they need? The idea is to make them apply filters before they are able to see any data. 

### Make the User Filter First

Let us create a sample PBI report using the GCSE exam scores dataset from the `mlmRev` package in R. The dataset is available [here](https://vincentarelbundock.github.io/Rdatasets/articles/data.html).

In this report, we provide the user with a table listing students' scores per school. The report includes a filter for school ID and a filter for student ID.

![2023-02-19-nudge-the-user-img02](/asset/screenshot/2023-02-19-nudge-the-user-img02.jpg)
<font size="-1"><center><span> GCSE exam scores listed in a PBI table </span></center></font>
<br>

Next, using DAX, we create a measure called *filter_check*. The goal of this measure is to return 1 if both an specific school ID and an specific student ID are chosen in filters, 0 otherwise. 

    {%raw%}

    filter_check =
    IF (
        AND (
            CALCULATE ( DISTINCTCOUNT ( Gcsemv[school] ), ALLSELECTED ( Gcsemv ) ) = 1,
            CALCULATE ( DISTINCTCOUNT ( Gcsemv[student] ), ALLSELECTED ( Gcsemv ) ) = 1
        ),
        1,
        0
    )

    {%endraw%}
<font size="-1"><center><span> DAX from measure <em>filter_check </em></span></center></font>
<br>

To create *filter_check* in the report, go to **Modeling**>**New Measure**. In the header bar, insert the above DAX code and press enter. 

![2023-02-19-nudge-the-user-img03](/asset/screenshot/2023-02-19-nudge-the-user-img03.jpg)
<font size="-1"><center><span> Creating <em>filter_check</em> in PBI </span></center></font>
<br>

Right after, use *filter_check* as a filter on the table visual. Begin by clicking on the table visual, and add *filter_check* as a table filter. Then, select the condition *is greater than 0*, and click on **Apply filter**. 

Given that neither a school ID nor a student ID has been chosen in filters, *filter_check* is equal to 0. Thus, the table shows no information. 

Finally, test this approach by choosing both school and student IDs. Notice that, normally, choosing only one of the two filters will render no results. Unless, of course, there is no more than one unique student ID per School and vice versa.

![2023-02-19-nudge-the-user-img04](/asset/screenshot/2023-02-19-nudge-the-user-img04.jpg)
<font size="-1"><center><span> Applying filters makes the table display results </span></center></font>
<br>

There are many versions of *filter_check*, depending on the report's requirements. For example, this measure can be change to require either a school ID or student ID. It can also allow for many school and student IDs. 

The sample report is available in [Github](https://github.com/moralescastillo/code_sample/blob/main/nudging_your_clients/gcse_exam_scores_england.pbix).
