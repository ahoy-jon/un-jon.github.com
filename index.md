---
layout: page
title: Hello World!
tagline: With some help from Joseph of Craftmanship.
---
{% include JB/setup %}


<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}"><h2>{{ post.title }}</h2></a></li>
  {% endfor %}
</ul>
