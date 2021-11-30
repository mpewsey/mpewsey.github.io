---
layout: default
title: Tags
---

<h1>Tags</h1>

{% assign sorted_tags = site.tags | sort %}

{% for tag in sorted_tags %}
  <div><a href='/tags/{{ tag[0] }}'>#{{ tag[0] }} ({{ tag[1].size }})</a></div>
{% endfor %}
