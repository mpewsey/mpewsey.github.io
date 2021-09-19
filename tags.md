---
layout: default
---

<h1>Tags</h1>

{% for tag in site.categories %}
  <div><a href='{{ site.url }}/tags/{{ tag | first }}'>#{{ tag | first }}</a></div>
{% endfor %}
