---
layout: page
title: Out and Back
---

<div>
<ul>
    {% for post in site.categories.out-and-back	%}
      <li><span>{{ post.date | date: "%Y-%m-%d" }} &raquo; </span><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
</div>