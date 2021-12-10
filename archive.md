---
layout: page
title: Archive
---

{%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}

This page provides a list of all posts in reverse chronological order.

<ul>
{% for post in site.posts %}
  <li>{{ post.date | date: date_format }} <a href='{{ post.url}}'>{{ post.title }}</a></li>
{% endfor %}
</ul>
