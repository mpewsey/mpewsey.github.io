---
layout: base
---

{% for post in site.posts limit: 10 %}

  <h2><a href='{{ post.url }}'>{{ post.title }}</a></h2>
  <p>{{ post.date | date_to_string }}</p>
  {{ post.excerpt }}

{% endfor %}
