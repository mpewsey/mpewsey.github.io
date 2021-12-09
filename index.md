---
layout: default
---

<h1>Recent Posts</h1>

{% for post in site.posts limit: 10 %}

  <h2><a href='{{ post.url }}'>{{ post.title }}</a></h2>
  <div class="post-info"><span class="post-date">{{ post.date | date_to_string }}</span>{% for tag in post.tags %} <a class="post-tag" href='/tags/{{ tag }}'>#{{ tag }}</a>{% endfor %}</div>

  {{ post.excerpt }}

  {% if post.image %}
  <img src="{{ post.image }}">
  {% endif %}

{% endfor %}
