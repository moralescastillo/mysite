---
layout: page
title: Tags
---

{% assign rawtags = "" %}
{% for post in site.posts %}
	{% assign ttags = post.tags | join:'|' | append:'|' %}
	{% assign rawtags = rawtags | append:ttags %}
{% endfor %}
{% assign rawtags = rawtags | split:'|' | sort %}

{% assign tags = "" %}
{% for tag in rawtags %}
	{% if tag != "" %}
		{% if tags == "" %}
			{% assign tags = tag | split:'|' %}
		{% endif %}
		{% unless tags contains tag %}
			{% assign tags = tags | join:'|' | append:'|' | append:tag | split:'|' %}
		{% endunless %}
	{% endif %}
{% endfor %}

Tags from blog entries:

<ul>
{% for tag in tags %}
	<li>
        <a href="/tag/{{ tag | slugify }}"> {{ tag }} </a>
    </li>
{% endfor %}
</ul>

{% comment %}

References: https://raw.githubusercontent.com/cbschuld/chrisschuld.com/master/tag.md

{% endcomment %}