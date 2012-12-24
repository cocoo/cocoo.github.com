---
layout: page
title: cocoo's Blog
tagline: 
---

<ul class="posts">
  {% for post in site.posts %}
  	{{ BASE_PATH }}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


