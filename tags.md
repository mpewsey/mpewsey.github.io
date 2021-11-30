---
layout: default
title: Tags
---

<h1>Tags</h1>

This page provides a list of all tags listed in alphabetical order.

{% assign sorted_tags = site.tags | sort %}

<ul>
{% for tag in sorted_tags %}
  <li><a href='/tags/{{ tag[0] }}'>#{{ tag[0] }} ({{ tag[1].size }})</a></li>
{% endfor %}
</ul>
