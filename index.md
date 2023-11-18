---
layout: default
title: Главная Страница
---

{% for post in site.posts %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.date | date_to_string }} - {{ post.excerpt }}</p>
{% endfor %}
