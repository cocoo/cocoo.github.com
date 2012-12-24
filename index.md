---
layout: page
title: cocoo dai's Blog
tagline: 
---

<ul class="posts">
  {% for post in site.posts %}
  	{{ BASE_PATH }}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


