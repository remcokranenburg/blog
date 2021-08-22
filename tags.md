---
layout: page
title: Tags
tagline: All tags used in posts
permalink: /tags.html
ref: tags
order: 1
---

{% for tag in site.tags %}
  <a href="{{ site.url }}{{ site.baseurl }}{{ tag.url }}">{{ tag.name }}</a>
{% endfor %}
