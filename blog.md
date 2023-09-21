---
layout: default
title: Blog Posts
---

## Blog Posts

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url }})
  - Author: {{ post.author }}
  - Date: {{ post.date | date: "%B %-d, %Y" }}
{% endfor %}
