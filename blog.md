---
layout: page
title: Blog
---

<div>
<ul>
    {% for post in site.categories.blog %}
      <li><span>{{ post.date | date: "%Y-%m-%d" }} &raquo; </span><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
</div>