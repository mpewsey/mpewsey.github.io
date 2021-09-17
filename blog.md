---
layout: default
---

<h1>Recent Posts</h1>

{% for post in site.posts limit: 10 %}

  <h2><a href='{{ post.url }}'>{{ post.title }}</a></h2>
  <p>{{ post.date | date_to_string }}{% for tag in post.categories %} <a href='{{ site.url }}/tags/{{ tag }}'>#{{ tag }}</a>{% endfor %}</p>
  {{ post.excerpt }}

{% endfor %}