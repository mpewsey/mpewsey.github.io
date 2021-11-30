---
layout: default
title: Archive
---

<h1>Archive</h1>

This page provides a list of all posts in reverse chronological order.

<ul>
{% for post in site.posts %}
  <li>{{ post.date | date_to_string }} <a href='{{ post.url}}'>{{ post.title }}</a></li>
{% endfor %}
</ul>
