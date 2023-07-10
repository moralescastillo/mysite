---
layout: post
title: 'Effortless Documentation with dbt: Streamlining Data Warehouse Documentation'
subtitle: Unlocking Clarity and Collaboration in Data Modeling
description: A step-by-step approach to documenting tables and columns, leveraging YAML and Markdown files. Learn how to create comprehensive documentation, including tests, with the help of Jinja scripts. Enhance collaboration, improve understanding, and ensure the clarity of your data models with dbt's powerful documentation capabilities
date: 2023-07-10 22:33:00 +0200
author: paulo
image: '/images/2023-07-10-effortless-documentation-dbt-img00.jpg'
image_caption: 'Photo by [Nana Smirnova](https://unsplash.com/@nananadolgo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/IEiAmhXehwE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)'
tags: [dbt, dwh]
feature:
---

When was the last time you looked at a data warehouse for the first time? Do you remember that feeling of frustration, not knowing what tables *orders_final_v1* contained? How about the difference between *user_uuid* and *user_id*? Any data practitioner can relate to such feelings.

Thankfully for us, dbt (Data Build Tool) has made documenting data warehouses a much easier task. All we need to do is include the documentation of our tables and columns in the schema YAML file. Then, all the information gets compiled into a neat HTML file.

### Documenting your Data Warehouse (DWH)

When working with data models in dbt, it is important to document your work to ensure that others can understand what you have built and how to use it.

Let's begin with the *models/schema.yml* file. This file will contain all the information about your tables. For example, consider the following documentation for the table *dim_album*:

<script src="https://gist.github.com/moralescastillo/d99bcb20b3591026bc9d54a9db0ee2ae.js"></script>
<font size="-1"><center><span> Pro tip: Use a folded block scalar, or ">", to fold new lines into spaces. This way, the YAML file is readable to the next person who will edit it </span></center></font>
<br>

After saving the above YAML, execute `dbt docs generate`. Finally, the command `dbt docs serve` will open your default browser and render your documentation.

![2023-07-10-effortless-documentation-dbt-img01](/images/2023-07-10-effortless-documentation-dbt-img01.jpg){:loading="lazy"}
<font size="-1"><center><span> Rendered version of dim_album documentation </span></center></font>
<br>


### Using a **docs** block

We can take documentation up a notch by using **docs** blocks. This way, we leverage a markdown file with Jinja scripts to generate an even more comprehensive document.

Let's begin by creating the file *models/docs.md*. Within this file, we place our markdown documentation, enclosed by the Jinja functions.

<script src="https://gist.github.com/moralescastillo/0b6b49c38f66f8c71d69590670b7da94.js"></script>
<font size="-1"><center><span> Example markdown file to supplement dim_album's documentation. Notice that, the holder's reference name is dictated by {% raw %} {% docs dim_album %} {% endraw %}</span></center></font>
<br>

We then reference this *models/docs.md* markdown file using Jinja within the schema YAML:

<script src="https://gist.github.com/moralescastillo/f3a2f3661ca72007d2c19b13dbb9a8b1.js"></script>
<font size="-1"><center><span> Replacing the model's description by the Jinja holder {% raw %} "{{ doc('dim_album') }}" {% endraw %} </span></center></font>
<br>

This approach allows you to create documentation with all the flexibility expected from a markdown file.

![2023-07-10-effortless-documentation-dbt-img02](/images/2023-07-10-effortless-documentation-dbt-img02.jpg){:loading="lazy"}
<font size="-1"><center><span> Render version of dim_album's documentation leveraging a docs block</span></center></font>
<br>


### What about Tests?

Whenever a generic test has been called on a column within the schema YAML file, it will appear in the documentation under the corresponding column.

![2023-07-10-effortless-documentation-dbt-img03](/images/2023-07-10-effortless-documentation-dbt-img03.jpg){:loading="lazy"}
<font size="-1"><center><span> The generic test for uniqueness has been called on the column album_id. It is configured in the YAML schema file and rendered by dbt docs serve </span></center></font>
<br>

Similarly to generic tests, singular tests are custom pieces of SQL that are executed against our dbt models. They are stored within the *tests* folder and get executed alongside generic tests.

One important aspect of documentation is describing the tests that you have written to validate your data models. At the moment, dbt will create a documenation page for singular tests. It will show you the SQL behind the singular test, as well as the model that is being tested under dependencies.

However, dbt does not yet allow you to fill in the [description box in a singular test's documentation](https://github.com/dbt-labs/dbt-core/issues/2578). A workaround is to describe the test and reference its documentation under the model's documentation page. Simply use the test's page URL suffix in the *docs.md* file.

<script src="https://gist.github.com/moralescastillo/f62d912d0c8d42502a7ac2ebcd57923d.js"></script>
<font size="-1"><center><span> Including description for older_album_test and a reference link to its own documentation page </span></center></font>
<br>

Voila! The result is your model's documentation page, which lists a model's generic and singular tests. It is always useful to append a query that helps the reader to identify and correct the error raised by the test. And last but not least, do not forget to use clear and concise language whenever you can. Happy querying.

