---
layout: page
title: Tags
---

This page provides a list of all tags in alphabetical order.

{% assign sorted_tags = site.tags | sort %}

<ul>
{% for tag in sorted_tags %}
  <li><a href='/tags/{{ tag[0] }}'>#{{ tag[0] }} ({{ tag[1].size }})</a></li>
{% endfor %}
</ul>
